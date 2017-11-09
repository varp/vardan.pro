---
layout: post
title: Hacking SharePoint 2010 DB Performance
date: '2015-02-08 20:44:00'
tags:
- sharepoint-2010
- ms-sql-2008-r2
- windows-server-2008-r2
---

<h2>Intro</h2><p>We are using SharePoint Foundation 2010 SP2 as <a rel="nofollow" href="http://en.wikipedia.org/wiki/Document_management_system"></a><a rel="nofollow" href="http://en.wikipedia.org/wiki/Document_management_system">EDM</a> system in our organization. We faced with a performance issue, after 3 years of using SharePoint 2010.</p><h2>The Issue Context</h2><p style="outline-width: 0px !important; -webkit-user-select: text !important;">Every employee in our organization has a pool of tasks to do. The pool of tasks is implemented as a SharePoint list. A task may has a dozen of executors. The executors leaving comments to the task overtime. Therefore, version history of the task (a list item) is growing and growing. On some tasks, we have about 2500 version history items. A SharePoint
2010 wasting about 150000 ms to retrieve these tasks.</p><h2>Solving The Issue</h2><p><b>WARNING! YOU CAN LOOSE <a rel="nofollow" href="https://support.microsoft.com/kb/841057/en-us?wa=wsignin1.0">SUPPORT</a> ON YOUR COPY OF SHAREPOINT! </b></p><p>Open SQL Server Management Studio and run following script on a content database of your SharePoint instance:</p>
```sql
USE [WSS_Content];
GO
create nonclustered index AllUserData_tpID on dbo.AllUserData ( tp_ID ASC );
create nonclustered index AllUserData_tp_ListId on dbo.AllUserData ([tp_ListId] ASC);
create nonclustered index AllUserData_tp_SiteId on dbo.AllUserData ([tp_SiteId] ASC);
create nonclustered index AllUserData_tp_RowOrdinal on dbo.AllUserData ([tp_RowOrdinal] ASC);
create nonclustered index AllUserData_tp_DeleteTransactionId on dbo.AllUserData ([tp_DeleteTransactionId] ASC);
create nonclustered index AllUserData_tp_Level on dbo.AllUserData ([tp_Level] ASC);
create nonclustered index AllUserData_tp_IsCurrentVersion on dbo.AllUserData ([tp_IsCurrentVersion] ASC );
GO
```
<p>After that, I have achieved increasing of the DB performance up to 100 times.</p>
