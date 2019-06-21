# WAD Analyzer
It's a self-help diagnostic utility to analyze Azure Cloud Service Guest VM logs and automatically provides you guidance to solve issues faster. The command-line tool has the capability of analyzing these application and azure related logs:

- IIS Logs
- HTTPErr Logs
- Failed Request Traces
- Application and System Event Viewer Logs
- WaHostBootstrapper Logs
- AppAgentRuntime Logs
- WaAppAgent Logs
- Azure Runtime Event Viewer Logs

Apart from analyzing cloud service application and runtime issues, this command-line utility provides you holistic information about the role properties and the guest VM configuration.

#### Additional Features

- Plot data on a chart

![Chart](https://github.com/prchanda/wada/blob/images/Chart.png)

- Download data or copy to clipboard

![CopyDownloadData](https://github.com/prchanda/wada/blob/images/CopyDownloadData.png)

- Filter or search data

![FilterSearch](https://github.com/prchanda/wada/blob/images/FilterData.png)

#### Prerequisites

- .NET Framework 4.7.2 must be installed in the system.
- Internet connectivity should be there in the system for CSS and JS functionality.

### How to use WAD Analyzer?
----------------------------

**Usage : WadAnalyzer [-c] [-p folder] [-ts] [-te] [-d {Full} [App|Azure]] [-s]**


| Parameter | Description |
| --- | --- |
| **`-c`** | Configure the analyzers of a diagnostic engine |
| **`-p`** | Guest VM Log folder location |
| **`-ts`** | Start timestamp "yyyy-MM-dd HH:mm:ss" (UTC) from which diagnostic engine will analyze the logs |
| **`-te`** | End timestamp "yyyy-MM-dd HH:mm:ss" (UTC) till which diagnostic engine will analyze the logs |
| **`-d`** |  Set the diagnostic engines (default is Full which includes both Application and Azure Runtime log analysis) |
| **`-s`** | Show status of the analyzers |
| **`-h`** | Print this help |


### Examples
------------

Analyze only application related logs from the directory path : 'C:\GuestVMLogs'

 **`WadAnalyzer.exe -p "C:\GuestVMLogs" -d App`**
 
Analyze logs within a particular timeframe i.e from '2019-02-06 17:35:00' UTC to '2019-02-06 21:05:00' UTC

 **`WadAnalyzer.exe -p "C:\GuestVMLogs" -ts "2019-02-06 17:35:00" -te "2019-02-06 21:05:00"`** 


### Frequently Asked Questions
------------------------------

#### How do I collect the azure runtime and application related logs before even analyzing them using WAD Analyzer ?

For Guest OS Family 4 and above, simply RDP to the guest VM and run CollectGuestLogs.exe, however in case of Guest OS family 2 and 3 you need to run SDP packages. Please refer [this](https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/) MSDN blog for more info. You can also manually collect these logs from the compute nodes for analysis.

#### I want to analyze only IIS logs. How can I do that ?

Using **`-c`** parameter you can turn ON/OFF the analyzers of a diagnostic engine. Press 'ESC' key or close the WADA Configurator window to save the settings.

![WADA Configurator](https://github.com/prchanda/wada/blob/images/Configurator.png)

#### Why am I not getting any output from an analyzer in WAD Analyzer report even though it is enabled ?

There could be few reasons why you may not get an analyzer's output in the report:

- Log files of a particular analyzer type are missing in the folder path specified by **`-p`** parameter.
- Analyzer's conditions are not met.
- Timeframe specified by **`-ts`** and **`-te`** parameters doesn't include any logs to be analyzed.

To be find out the exact cause you can check **WadAnalyzer.log** present in the same directory where WadAnalyzer.exe is present. This log tracks the progress of WAD Analyzer and captures all the vital events like errors encountered, number of input/output records processed by an analyzers, etc. Look for these kind of events to track down the cause:

`2019-06-03 09:18:13,334 INFO Runtime_Analyzer.AppAgentRuntime_Analyzer -`**`No AppAgentRuntime log files detected`**`in the path 'C:\GuestVMLogs'`

`2019-06-03 09:22:10,380 INFO Runtime_Analyzer.AppAgentRuntime_Analyzer - Input records processed = 1653,`**`Output records = 0`**

#### I am getting the below error while running WAD Analyzer tool. Why ?

_`System.Runtime.InteropServices.COMException: Retrieving the COM class factory for component with CLSID {8CFEBA94-3FC2-45CA-B9A5-9EDACF704F66} failed due to the following error: 80040154 Class not registered (Exception from HRESULT: 0x80040154 (REGDB_E_CLASSNOTREG))`_

Since WAD Analyzer utilizes LogParser tool for analyzing the logs, it needs to register the COM objects of LogParser.dll first. In order to register LogParser.dll you need to run WAD Analyzer tool in Admin or Elevated mode _atleast once_. For subsequent runs you can use non-elevated mode to analyze the logs. You can also run the below command in Admin or Elevated mode to register LogParser.dll:

**`regsvr32 "Path of LogParser.dll present under Resources folder"`**

#### CSS for WAD Analyzer report is not loading correctly or additional features like plotting graph, searching or downloading data are not working as expected. What went wrong ?

As of now all the additional features and CSS are supported by resources that are externally hosted in CDN repositories. Hence the system from where you are running the tool must have internet connectivity so that the report generated by WADA tool works properly.

#### Why am I getting the below exception and what should I do to solve it ? 

_`System.Runtime.InteropServices.COMException (0x8007000D): Error parsing query: Log row too long
LogFile "D:\WAD Analyzer\GALogs\RuntimeAndAgent\WaAppAgent.000.log", Row number 7 [The data is invalid.]
at MSUtil.LogQueryClassClass.ExecuteBatch(String szQuery, Object pObjectInputContext, Object pObjectOutputContext)`_

You may typically encounter this error when the log file rows are huge in size and it exceeds the default allocated buffer size of Log Parser which is 0x00785111. You need to increase the value of registry key **CSVInMaxRowSize** in order to overcome this error. Refer [this](https://blogs.technet.microsoft.com/rmilne/2012/07/05/how-to-fix-log-parser-log-row-too-long/) article for more information on this topic.
