---
layout: post
title:  "Windows PowerShell"
date:   2015-05-01
tags: [Windows,Java, Play Framework]
---

In our daily activities we received the responsibility to monitor some windows services with their required status.

A list of servers with their respective services we had to monitor had been given to us. The fast solution was to create an Excel file to register in it any failure. We had to verify during morning, at the middle of the day and at the end of the day. The number of services were about 30, so this process of verification was very tedious.
This tedious process made us think on a solution, and it was to use Windows PowerShell at the first instance. We invested a bit of time studying how to use PowerShell. Basically we had to know how to create the script to verify the status of the service. During our daily work we found that to know the CPU and Memory usage were very useful too, and then we created the scripts to know these usages.

Good! We had created these PowerShell scripts although we had to do some additional tasks such as schedule the time to verify the status, log any unexpected result and send notification for any unexpected result. The complete solution is showed into the next draft diagram: ![Draft Solution]({{site.url}}/assets/2015-05-01/draftSolution.png)

Of course we focus only with the solution with Windows PowerShell, first `showing how we created the PowerShell scripts`, second `defining an API`, and finally creating the `implementation of this API using Play framework`.

I am using a computer with Windows 7 installed due to PowerShell is used only on Windows environments. We don’t want get into the PowerShell details because the most important point here is the solution’s implementation.


## PowerShell scripts
We are going to create three script. The first for obtaining the CPU usage, the second for the memory usage and the third the service status.

### CPU Usage
Into your favorite directory, in my case I use `D:\code\powershell` path, create a file name `cpu.ps1`. Right click over it. Into the contextual menu select   `Edit`. The next window will be opened: ![PowerShell ISE]({{site.url}}/assets/2015-05-01/PowerShell01.png)

This window is named Windows PowerShell Integrated Scripting Environment (`Windows PowerShell ISE`).
Add the next code and save your file:

{% highlight PowerShell %}

param([String]$hostName="localhost")

write-host $hostName
Get-WmiObject -computername $hostName win32_processor | Measure-Object -property LoadPercentage -Average | Select Average 

{% endhighlight %}

Now the window looks like: ![PowerShell ISE with CPU code]({{site.url}}/assets/2015-05-01/PowerShell02.png)

Test your script ![PowerShell execution disabled]({{site.url}}/assets/2015-05-01/PowerShell03.png)

The last problem happens because first you must execute the next command and select “Y”:
{% highlight sh %}
Set-ExecutionPolicy RemoteSigned
{% endhighlight %}
![PowerShell execution enabled]({{site.url}}/assets/2015-05-01/PowerShell04.png)
`
Run it again then the next is the result: ![PowerShell running cpu.ps1]({{site.url}}/assets/2015-05-01/PowerShell05.png)

You can run it from the Windows PowerShel ISE: ![PowerShell running cpu.ps1 into ISE]({{site.url}}/assets/2015-05-01/PowerShell06.png)

If you don’t specify the hostname, the default value is used `(localhost, my computer)` But you can verify other computer if you have the rights over it: ![PowerShell running cpu.ps1 into ISE with other computers]({{site.url}}/assets/2015-05-01/PowerShell07.png)

### CPU Memory Usage
Into the same directory (“D:\code\powershell”) create a file name `memory.ps1`. Right click over it. Into the contextual menu select “Edit”, add the next code, save and test it:

{% highlight PowerShell %}

param([String]$hostName="localhost")

write-host $hostName

gwmi -Class win32_operatingsystem -computername $hostName | 
Select-Object @{Name = "MemoryUsage"; Expression = {“{0:N2}” -f ((($_.TotalVisibleMemorySize - $_.FreePhysicalMemory)*100)/ $_.TotalVisibleMemorySize) }}

{% endhighlight %}

The next window is showed: ![PowerShell running memory.ps1]({{site.url}}/assets/2015-05-01/PowerShell08.png)

### Service Status
Follow the same procedure but now with the file named `service.ps1` using the next code:
{% highlight PowerShell %}

param([String]$serviceName="" ,[String]$hostName="localhost")
if($serviceName -eq '') {
    get-service -computername $hostName| select Status,Name,DisplayName | Format-List
} else {
    get-service -computername $hostName | where-object {$_.Name -match "^$serviceName{1}$"} | select Status, Name, DisplayName | Format-List
} 

{% endhighlight %}

Into the Windows PowerShell ISE will show: ![PowerShell running service.ps1]({{site.url}}/assets/2015-05-01/PowerShell09.png)

Of course you can specify any specific server and service: ![PowerShell running specific service]({{site.url}}/assets/2015-05-01/PowerShell10.png)


## API Definition
We are at the moment we have to define the contract so that the users can use our application. We need to declare how to access to our application and how to obtain the required information. ![API Definition]({{site.url}}/assets/2015-05-01/api01.jpg)

Why not use only the created scripts?

The only constant with software development is the `change`, so when we create a solution we always must think in terms of scalability, standardization, modularity, security, and so on.

Defining an API we assure don't change the form to request any resource.

Let's see an example: I want to know the CPU usage of serverX. I use the defined API to retrieve the required information. The implementation executes the PowerShell script. Now the company has decided changes the old serverX based on Windows platform to Red Hat Linux Enterprise server. Of course the implementation changes because now I am not going to use PowerShell. I am going to use Bash scripts. But the user don't worry about this because the API is the same. Only the implementation has changed. Or we now receive the task to monitor not only the Windows based computers, we receive a list of Linux based computers, then we change our implementation to support these new computers.

The most useful standard due to their benefits is the HTTP REST services. Then we are going to create our API based on REST.

Our API is defined:
{% highlight text %}
GET /healthCheckServer/cpu?{hostName=hostname}

GET /healthCheckServer/services?{hostName=hostname}&{serviceName=servicename}
{% endhighlight %}

The first one is used for obtaining the usage of CPU and usage of the memory. Using one request we can obtain information of both usages. If the parameter is not defined, then it returns the information of localhost. Otherwise retrieves the information of the specific server.

The second one is used for obtaining the services statutes. If the hostName and the serviceName are not used, it returns the information of localhost. If only the hostName is used then it returns all the statutes of all the services of the hostName. If only the serviceName is used then returns all the statuses of all the services of the server. If you use the serviceName and hostName then it returns only the service ant its status.

Because both request are queries, we use GET HTTP method.

## Implementation using Play Framework
Of course our construction must be based using some design ![Implementation design]({{site.url}}/assets/2015-05-01/apiImplementation01.png)
The first step is to create our workspace. In my case I create the next directory: 

{% highlight text %}
D:\code\activator\wsHealthCheckServer
{% endhighlight %}

Create a project with Play named `HealthCheckServer`: ![Implementation design]({{site.url}}/assets/2015-05-01/apiImplementation02.png)

![Implementation design]({{site.url}}/assets/2015-05-01/apiImplementation03.png)

After you have created the project and you can visualize it on Eclipse, we can start.

Open the `application.conf` file, add the next line and save it:

{% highlight text %}
powerShell.scripts.path="D:\\code\\powershell\\"
{% endhighlight %}

Create the `ServiceStatus.java` using the next code: 
{% highlight java %}
package models;

public enum ServiceStatus {
	Running, Stopped, Paused, StartPending, StopPending;
}
{% endhighlight %}

Create the `CPU.java` using the next code:
{% highlight java %}
package models;

public class CPU {
	private float usage;
	private float memory;

	public float getUsage() {
		return usage;
	}

	public void setUsage(float usage) {
		this.usage = usage;
	}

	public float getMemory() {
		return memory;
	}

	public void setMemory(float memory) {
		this.memory = memory;
	}
	
}
{% endhighlight %}

Create the `PowerShell.java` using the next code:
{% highlight java %}
package utilities.windows;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;

import javassist.NotFoundException;
import play.Logger;
import models.CPU;
import models.Service;
import models.ServiceStatus;

import com.typesafe.config.ConfigFactory;

public class PowerShell {
	private static String POWERSHELL_CMD = "cmd /C C:/WINDOWS/system32/WindowsPowerShell/v1.0/powershell.exe ";
	private static enum CPUIndicatorType {USAGE, MEMORY};
	private static String SCRIPT_PATH = ConfigFactory.load().getString("powerShell.scripts.path");
	private static String CPU_USAGE_SCRIPT_NAME    = SCRIPT_PATH + "cpu.ps1";
	private static String MEMORY_USAGE_SCRIPT_NAME = SCRIPT_PATH + "memory.ps1";
	private static String SERVICE_SCRIPT_NAME      = SCRIPT_PATH + "service.ps1";
		
	public static Set<Service> getServices(String hostName, String serviceName) throws IOException {
		Runtime runtime = Runtime.getRuntime();
		StringBuilder cmds = new StringBuilder();
		cmds.append(POWERSHELL_CMD);
		cmds.append(SERVICE_SCRIPT_NAME);
		cmds.append(" -hostname ");
		cmds.append("\"").append(hostName).append("\"");
		if (!isEmpty(serviceName)) {
			cmds.append(" -serviceName ");
			cmds.append("\"").append(serviceName).append("\"");
		}
		String line = "";
		Set<Service> services = new HashSet<Service>();
		Logger.debug("cmds = >>" + cmds + "<<");
		Process proc = runtime.exec(cmds.toString());
		proc.getOutputStream().close();
		InputStream inputstream = proc.getInputStream();
		InputStreamReader inputstreamreader = new InputStreamReader(inputstream);
		BufferedReader bufferedreader = new BufferedReader(inputstreamreader);
		ServiceStatus newServiceStatus      = ServiceStatus.Paused;
		String        newServiceName        = "";
		String        newServiceDisplayName = "";
		while ((line = bufferedreader.readLine()) != null) {
			if (isEmpty(line)) {
				continue;
			}	
			String[] items = line.split(":");
			String key   = items[0].trim();
			String value = items[1].trim();
			switch (key) {
			case "Status":
				newServiceStatus = ServiceStatus.valueOf(value);
				break;
			case "Name":
				newServiceName = value;
				break;
			case "DisplayName":
				newServiceDisplayName = value;
				Service newService = new Service(newServiceName, newServiceStatus, newServiceDisplayName);
				services.add(newService);
				newServiceStatus      = ServiceStatus.Paused;
				newServiceName        = "";
				newServiceDisplayName = "";				
				break;
			default:
				break;
			}
		}
		return services;
	}
	
	public static CPU getCPU(String hostName) throws IOException, NotFoundException {
		float memory = getCPUIndicatorValue(hostName, CPUIndicatorType.MEMORY);
		float usage = getCPUIndicatorValue(hostName, CPUIndicatorType.USAGE);
		CPU cpu = new CPU();
		cpu.setMemory(memory);
		cpu.setUsage(usage);
		return cpu;
	}
	
	private static float getCPUIndicatorValue(String hostName, CPUIndicatorType type) throws IOException, NotFoundException {
		Runtime runtime = Runtime.getRuntime();
		StringBuilder cmds = new StringBuilder();
		cmds.append(POWERSHELL_CMD);
		switch (type) {
		case USAGE:
			cmds.append(CPU_USAGE_SCRIPT_NAME);
			break;
		case MEMORY:
			cmds.append(MEMORY_USAGE_SCRIPT_NAME);
			break;
		default:
			throw new RuntimeException("CPU indicator type was not expected: " + type.name());
		}
		cmds.append(" -hostName ");
		cmds.append(hostName);
		String line = "";
		Process proc = runtime.exec(cmds.toString());
		proc.getOutputStream().close();
		InputStream inputstream = proc.getInputStream();
		InputStreamReader inputstreamreader = new InputStreamReader(inputstream);
		BufferedReader bufferedreader = new BufferedReader(inputstreamreader);
		boolean valueIsFound = false;
		float value = 0;
		while ((line = bufferedreader.readLine()) != null) {
			try {
				value = Float.parseFloat(line.trim());
				valueIsFound = true;
			} catch (NumberFormatException e) {
				continue;
			}
		}
		if (!valueIsFound) {
			StringBuilder errorMessage = new StringBuilder("Information of CPU memory/usage");
			errorMessage.append(" for host ").append(hostName).append(" was not found");
			throw new NotFoundException(errorMessage.toString());
		}
		return value;
	}

	public static boolean isEmpty(String string) {
		if (string != null && !string.trim().equals("")) {
			return false;
		} else {
			return true;
		}
	}
}
{% endhighlight %}

If you have problems to execute this class, you can replace the first line into the PowerShell class using the next code:
{% highlight java %}
	private static String POWERSHELL_CMD = "cmd /C C:/WINDOWS/system32/WindowsPowerShell/v1.0/powershell.exe  -ExecutionPolicy -noprofile -noninteractive ";
{% endhighlight %}

Create the `Service.java` using the next code:
{% highlight java %}
package models;

public class Service implements Comparable<Service> {
    private String name;
	private ServiceStatus status;
	private String displayName;
	
	public Service(String name, ServiceStatus status, String displayName) {
		this.name = name;
		this.displayName = displayName;
		if (status == null) {
			this.status = ServiceStatus.Running;
		} else {
			this.status = status;
		}
	}

	public String getName() {
		return name;
	}

	public ServiceStatus getStatus() {
		return status;
	}
	
	public String getDisplayName() {
		return displayName;
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Service other = (Service) obj;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
	
	@Override
	public int compareTo(Service o) {
		return this.name.compareTo(o.name);
	}

	@Override
	public String toString() {
		return "Service [name=" + name + ", status=" + status
				+ ", displayName=" + displayName + "]";
	}
    
}
{% endhighlight %}

Modify the `Application.java` adding the next two methods:
{% highlight java %}
	public static Result getCPU(String hostName) {
		CPU cpu;
		try {
			cpu = PowerShell.getCPU(hostName);
		} catch (NotFoundException e) {
			e.printStackTrace();
			return Results.internalServerError(e.getMessage());
		} catch (IOException e) {
			e.printStackTrace();
			return Results.internalServerError(e.getMessage());
		}
		return ok(Json.toJson(cpu));
	}
	
	public static Result getServices(String hostName, String serviceName) {
		Set<Service> services;
		try {
			services = PowerShell.getServices(hostName, serviceName);
		} catch (IOException e) {
			e.printStackTrace();
			return Results.internalServerError(e.getMessage());
		}
		return ok(Json.toJson(services));
	}   
{% endhighlight %}

Modify the `routes` file adding the next two lines:
{% highlight text %}
GET /healthCheckServer/cpu      controllers.Application.getCPU(hostName:String ?= "localhost")
GET /healthCheckServer/services controllers.Application.getServices(hostName:String ?= "localhost", serviceName:String ?= "")
{% endhighlight %}

The application is ready!

Let’s go to verify how it is working:
{% highlight text %}
http://localhost:9000/healthCheckServer/cpu?hostName=A229LBJX
{% endhighlight %}
![checking the cpu usage](/images/2015-05-01/apiImplementation04.png)

{% highlight text %}
http://localhost:9000/healthCheckServer/services?hostName=A229LBJX&serviceName=CVPND
{% endhighlight %}
![checking the cpu usage](/images/2015-05-01/apiImplementation05.png)

{% highlight text %}
http://localhost:9000/healthCheckServer/services?hostName=A229LBJX
{% endhighlight %}
![checking the cpu usage](/images/2015-05-01/apiImplementation06.png)

The parameters are according to my environment. You can use the values according to your server or other servers you have the rights to access them.

If you want to know more about the use of Windows PowerShell, you can read the book [Windows PowerShell in Action, Second Edition][1].

We reached the end of this practical use of Windows PowerShell. 


[1]: http://www.amazon.com/gp/product/1935182137/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1935182137&linkCode=as2&tag=wwwalbeviacom-20&linkId=XEYTBD46LFLQF2Y2


