---
layout:     post
title:      "Instruments Help 中文翻译"
subtitle:   "Instruments 使用中文指南"
date:       2018-10-26 00:35:00
author:     "Dan"
header-img: "img/15404856362265.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Instruments 
    - Xcode
---


# Instruments Help
## About Instruments
### Instruments Overview
Instruments is a powerful and flexible performance-analysis and testing tool that’s part of the Xcode tool set. It’s designed to help you profile your iOS, watchOS, tvOS, and macOS apps, processes, and devices in order to better understand and optimize their behavior and performance. Incorporating Instruments into your workflow from the beginning of the app development process can save you time later by helping you find issues early in the development cycle.

*profile:  an analysis (often in graphical form) representing the extent to which something exhibits various characteristics
 一种分析(通常以图形形式)，表示某物表现出不同特征的程度*

Instruments是一个功能强大且灵活的性能分析和测试工具，属于Xcode工具集的一部分。它旨在帮助您分析您的iOS、watchOS、tvOS和macOS应用程序、流程和设备，以便更好地理解和优化它们的行为和性能。从应用程序开发过程的一开始就将工具合并到您的工作流中，可以帮助您在开发的早期发现问题，从而节省您的时间。

In Instruments, you use specialized tools, known as instruments, to trace different aspects of your apps, processes, and devices over time. Instruments collects data as it profiles, and presents the results to you in detail for analysis.
在Instruments中，您使用专门的工具(Instruments)来跟踪应用程序、流程和设备的不同方面的问题。Instruments收集数据进行分析，然后把分析结果以图表的形式向您展示，以便您进行详细分析。

Unlike other performance and debugging tools, Instruments allows you to gather widely disparate types of data and view them side by side. This makes it easier to identify trends that might otherwise be overlooked. For example, your app may exhibit large memory growth caused by multiple open network connections. By using the Allocations and Connections instruments together, you can identify connections that are not closing and thus resulting in rapid memory growth.

与其他性能和调试工具不同，Instruments使您能够收集广泛不同类型的数据并并排查看它们。这使得识别可能被忽略的趋势变得更加容易。例如，您的应用程序可能会由于打开了多个网络连接而出现较大的内存增长。通过将Allocations和Connections工具一起使用，您可以识别没有关闭的连接，从而找到导致内存快速增长的原因。


By using Instruments effectively, you can:
为了有效使用Instruments，你可以这么做:

*  Examine the behavior of one or more apps or processes
  检查一个或多个应用程序或进程的行为

*  Examine device-specific features, such as Wi-Fi and Bluetooth
   检查特定设备的特性，比如Wi-Fi和蓝牙

*  Perform profiling in a simulator or on a physical device
    在模拟器或物理设备上进行性能分析

*  Track down problems in your source code
    跟踪源代码中的问题

*  Conduct performance analysis on your app
    对应用程序进行性能分析

*  Find memory problems in your app, such as leaks, abandoned memory, and zombies
    在你的应用程序中发现内存问题，比如内存泄露和僵尸对象

*  Identify ways to optimize your app for greater power efficiency
    找出优化应用程序的方法，以提高电源效率

*   Perform general system-level troubleshooting
    执行一般的系统级故障排除

*   Save instrument configurations as templates
    将instrument配置保存为模板

*   Although it’s embedded within and may be used with Xcode, Instruments is a separate app, which may be used independently as needed.
    虽然Instruments是内嵌的，与Xcode一起使用，但是Instruments是一个独立的应用程序，可以根据需要独立使用。


### About the trace document  关于跟踪文档.    
A trace document is used to organize and configure instruments for profiling, initiate data collection, and view and analyze the results. You create a new trace document by launching Instruments and choosing a profiling template or by initiating profiling from Xcode, the Dock, or the command line. You can also save and reopen trace documents in which you’ve configured instruments and collected data. A trace document can contain a lot of extremely detailed information, and this information is presented to you through a number of panes and areas.

跟踪文档用于组织和配置用于分析，初始化数据收集以及查看和分析结果的instruments环境。通过启动Instruments并选择一个概要分析模板，或者通过Xcode、Dock或命令行启动概要分析，您可以创建一个新的跟踪文档。您还可以保存和重新打开已经配置好了的Instruments环境和收集的跟踪文档数据。跟踪文档可以包含许多非常详细的信息，这些信息通过许多窗格和区域呈现给您。

![](/img/15404788970539.png)

### About the trace document toolbar. 

The toolbar of the trace document lets you start, pause, and stop data profiling, add instruments, hide and show panes, and more.

The toolbar includes the following main elements:

* Profiling controls: Allow you to record, pause, and stop data collection.

* Target device list: Allows you to select the device on which you wish to profile.

* Target process list: Allows you to select the process or processes to profile.

* Activity viewer: Shows the number of runs for the trace document and the elapsed time of the current trace.

* Add Instrument button (+): Shows or hides the instruments Library palette, which contains a complete list of available instruments. From here, you can select individual instruments and add them to your trace document.

* View buttons (![](/img/15404819260607.jpg)): Hide or show the detail pane and inspector.

![](/img/15404818145203.jpg)


### About the trace document timeline pane

The timeline pane displays a graphical summary of the data recorded for a given trace. In this pane, each instrument, CPU core, or thread has its own “track,” which provides a graphical chart of the data collected.

Although this pane’s information is read-only, you can scroll through data, select specific areas for closer examination, and insert flags to highlight points of interest. You can change how graphical information is displayed here by adjusting the zoom level or by changing the display settings of individual instruments using the *display configuration popover*. An optional filter bar provides controls for filtering the data displayed by an instrument. For example, the Sampler instrument can filter the data by thread.

You can pin timelines to the bottom of the pane to keep important information visible as you investigate issues. For example, you can pin the Leaks instrument to the bottom of the pane, and then scroll through the other instruments to find the possible causes for a leak. Pinned instruments are not preserved between runs.

![](/img/15404820808853.png)

**Strategy views**

Click the strategy buttons in the filter bar to display instruments, CPU core, thread data, or all three items in the timeline.

![](/img/15404821151025.png)

**All**: Show data for instruments, threads, and CPUs in the timeline.

**CPUs**: Displays a list of CPU cores, along with their usage over time, in the timeline pane. Only available when a trace document contains instruments that record CPU data.

![](/img/15404821630483.jpg)

**Instruments**: Displays a list of instruments and their corresponding data in the timeline pane.

If you select an instrument in the list, you can delete it or configure it in the inspector pane. The instruments list is visible by default when you create a trace document.

![](/img/15404821976600.jpg)

**Threads**: Displays a list of threads and their utilization data in the timeline pane. Only available when a trace document contains instruments that record thread data.

![](/img/15404822229378.jpg)

### About the display configuration popover

Use the *display configuration popover* to configure what data is displayed in the timeline and to configure the format of the displayed data.

The settings available in the display configuration popover vary, depending on the instrument. These settings can help you:

* Control the data that appears in the timeline. For example, the Activity instrument allows you to toggle the display of total threads, VM size, and several other system statistics.

* Adjust how recorded information is represented in the timeline view. For example, an instrument may allow you to switch between a peak graph and a block graph.

To open the popover, click the instrument icon in the trace document timeline pane. Instruments that support a display configuration popover show an indicator when the pointer is moved over the icon.

![](/img/15404823036914.png)

### About the trace document detail pane

The detail pane shows detailed information about the data collected by the instruments in your trace document. Select an individual instrument in the timeline pane to see the data it collected while profiling.

The detail pane consists of three main areas, the navigation bar, the collected data area, and the filter and configuration bar.

![](/img/15404823630723.jpg)

**Navigation bar** 

The navigation bar at the top of the detail pane helps you browse through collected data.

You can use the navigation bar to switch between types of data and to navigate through different levels of data.

* Instrument: Icon of the currently selected instrument in the timeline pane. Click this to view the console for the instrument.

* Detail type list: Allows you to navigate between different types of data. The options displayed here vary, depending on the actively selected instrument. For many instruments, the list includes things like a summary of data, a call tree, and a console.

* Detail tree: Keep track of where you are in the hierarchy as you navigate through the data in the detail pane. Click a branch of the tree to move back up the hierarchy to the corresponding data.

![](/img/15404823921127.jpg)

**Collected data area** 

The collected data area shows you all of the data for the selected instrument, typically in tabular format. The content displayed here varies significantly from instrument to instrument. For example, the Activity Monitor instrument displays process, CPU, and thread information, and much more.

![](/img/15404824399078.jpg)


Often, individual symbols and data points within this area contain navigation buttons (), which appear when you move your pointer over them. You can click these buttons to move deeper into the data. As you do, the detail tree in the navigation bar updates to reflect where you are in the hierarchy.

![](/img/15404824581000.jpg)

**Filter and configuration bar**

The *filter and configuration bar* at the bottom of the detail pane helps you filter the collected data and configure how that data is displayed.

*  *Filter field*: Allows you to filter collected data for a specific term. Click the filter field’s menu for some additional filtering options. You can also filter collected data more extensively by adjusting display settings in the *display configuration popover*.

* *Display configuration controls*: Refine the displayed results by filtering, sorting, and data mining. The controls vary from instrument to instrument and can include menus for selecting different views of the results, and popovers for constraining the data that is shown.

![](/img/15404825592998.png)

### About the trace document inspector pane

The inspector pane contains information about the run that’s currently displayed in the detail pane. It includes information about the recording and, for some instruments, additional details about the selected data.

![](/img/15404829404563.jpg)

**Run information area**

The run information area shows information about the run displayed in the detail pane including the target, recording time, and the settings used for the recording.

![](/img/15404829557412.png)

**Extended detail area**

The extended detail area is used to display additional instrument-specific information about selected data in the detail pane, such as a complete stack trace.

![](/img/15404830095466.jpg)

### About the Library palette

The Library palette provides a complete list of available instruments and lets you add them to your trace documents. Here, you can browse instrument descriptions and filter for a specific instrument.

![](/img/15404830422072.jpg)


To display the Library palette, choose Window > Library, press Command-L, or click the Add Instrument button (+) in the trace document toolbar.

### About the Flags palette

The Flags palette displays a list of any flags you may have applied in the timeline pane of the active trace document.

In the Flags palette, you can select a flag to quickly navigate to it in the timeline. You can also filter through a large list of flags for a specific one, show and hide your flags, and view timestamp information about your flags.

![](/img/15404830876770.jpg)

### About profiling templates

In Instruments, you use profiling templates to analyze your app. A profiling template is a trace document that has been preconfigured with instruments and settings for performing a common type of trace.

Profiling templates are available when you launch Instruments, create a new document, or initiate a trace from Xcode. You can also create your own templates if you have more advanced or custom needs.

![](/img/15404831217573.jpg)


### About the profiling template selection dialog

When Instruments launches, you’re presented with a list of profiling templates—files containing sets of preconfigured instruments—from which to choose. This list includes a set of standard templates as well as any custom templates you may have created.

The profiling template selection dialog consists of the following primary elements.

* Target device list: Click this to select the device on which you wish to profile.

* Target process list: Click this to select the process or processes to profile.

* Filter buttons: Click these to filter down the list of templates to display just the standard templates, custom templates, or recently used templates.

* Search field: Enter some text to quickly find the template you need. This searches template titles and descriptions.

* Template list: The list of profiling templates, which may be filtered if you clicked a filter button or entered search criteria.

* Template description: A short description of the currently selected profiling template, which can be helpful for determining whether the selected template meets your needs.

* Choose button: Click this to create a new profile document, based on the currently selected template.

* This button changes to Profile when you press the Option key. Click the Profile button to create a new document based on the currently selected template and immediately begin profiling the target process.

* Open button: Click this to open a previously saved profile document, rather than starting with a fresh template.

* Cancel button: Click this to close the template selection dialog.

Note: You can display profiling template selection dialog at any time by choosing File > New (or pressing Command-N).

![](/img/15404831684410.jpg)

### About the Preferences window

The preferences window is used to control various settings related to the behavior of Instruments.

In the preferences window, you can adjust general settings that pertain to startup, saving, and more. You can also adjust recording settings, CPU settings, and symbol preferences.

![](/img/15404832494938.jpg)
















