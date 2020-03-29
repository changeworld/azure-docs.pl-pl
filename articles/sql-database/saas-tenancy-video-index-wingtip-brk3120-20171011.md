---
title: Klip wideo z aplikacją SaaS SQL
description: W tym artykule indeksuje się różne punkty czasowe w naszym 81-minutowym filmie o projekcie aplikacji do najmu SaaS DB z konferencji Ignite, która odbyła się 11 października 2017. Możesz przejść do tej części, która Cię interesuje. Opisano co najmniej 3 wzory. Opisane są funkcje platformy Azure, które upraszczają tworzenie i zarządzanie.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067615"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Wideo indeksowane i adnotacjami dla aplikacji SaaS z wieloma dzierżawcami przy użyciu usługi Azure SQL Database

Ten artykuł jest indeksem z adnotacjami do lokalizacji czasu 81-minutowego filmu o modelach lub wzorcach dzierżawy SaaS. Ten artykuł umożliwia przeskakiwanie do tyłu lub do przodu w filmie, do którego część Cię interesuje. W klipie wideo opisano główne opcje projektowania aplikacji bazy danych z wieloma dzierżawami w bazie danych SQL Azure. Film zawiera wersje demonstracyjne, instruktaże kodu zarządzania, a czasami więcej szczegółów poinformowanych przez doświadczenie, niż może być w naszej pisemnej dokumentacji.

Wideo wzmacnia informacje w naszej pisemnej dokumentacji, znalezionej pod adresem: 
- *Koncepcyjne:* [Wzorce dzierżawy bazy danych SaaS z wieloma dzierżawami][saas-concept-design-patterns-563e]
- *Samouczki:* [Aplikacja Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

W klipie wideo i w artykułach opisano wiele faz tworzenia aplikacji z wieloma dzierżawami w usłudze Azure SQL Database w chmurze. Specjalne funkcje usługi Azure SQL Database ułatwiają tworzenie i implementowanie aplikacji z wieloma dzierżawami, które są łatwiejsze w zarządzaniu i niezawodnie wydajne.

Regularnie aktualizujemy naszą pisemną dokumentację. Film nie jest edytowany ani aktualizowany, więc ostatecznie więcej jego szczegółów może stać się nieaktualne.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekwencja 38 zindeksowanych zrzutów ekranu

W tej sekcji indeksuje lokalizację czasu dla 38 dyskusji w całym 81 minut wideo. Za każdym razem indeks jest opisywany za pomocą zrzutu ekranu z filmu, a czasami z dodatkowymi informacjami.

Za każdym razem indeks jest w formacie *h:mm:ss*. Na przykład druga lokalizacja czasu indeksowanego, oznaczona **jako Cele sesji,** rozpoczyna się od przybliżonej lokalizacji czasu **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompaktowe łącza do lokalizacji czasu indeksowanych wideo

Następujące tytuły są łączami do odpowiednich sekcji z adnotacjami w dalszej części tego artykułu:

- [1. **(Start)** Zjeżdżalnia powitalna, 0:00:03](#anchor-image-wtip-min00001)
- [2. Cele sesji, 0:03:11](#anchor-image-wtip-min00311)
- [3. Porządek obrad, 0:04:17](#anchor-image-wtip-min00417)
- [4. Wielodostępna aplikacja internetowa, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formularz internetowy aplikacji w akcji, 0:05:55](#anchor-image-wtip-min00555)
- [6. Koszt na dzierżawcę (skala, izolacja, odzyskiwanie), 0:09:31](#anchor-image-wtip-min00931)
- [7. Modele baz danych dla wielu dzierżawców: plusy i minusy, 0:11:59](#anchor-image-wtip-min01159)
- [8. Model hybrydowy łączy zalety MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Single-tenant vs multi-tenant: plusy i minusy, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pule są opłacalne dla nieprzewidywalnych obciążeń, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demonstracja bazy danych na dzierżawcę i hybrydowy ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Formularz aplikacji na żywo z dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB i nie DBA w zasięgu wzroku, 0:28:54](#anchor-image-wtip-min02854)
- [14. Przykład użycia basenu elastycznego MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Nauka od MYOB i innych dostawców oprogramowania, 0:31:36](#anchor-image-wtip-min03136)
- [16. Wzory komponują się do scenariusza E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical hybrydowy wielodostępny aplikacji SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. Przykładowa aplikacja Wingtip SaaS, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenariusze i wzorce zbadane w samouczkach, 0:49:10](#anchor-image-wtip-min04910)
- [20. Pokaz samouczków i repozytorium GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. Repozytorium GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Odkrywanie wzorców, 0:56:20](#anchor-image-wtip-min05620)
- [23. Inicjowanie obsługi administracyjnej najemców i dołączanie, 0:57:44](#anchor-image-wtip-min05744)
- [24. Udostępnianie obsługi administracyjnej dzierżawców i połączenie aplikacji, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demo skryptów zarządzania inicjowania obsługi administracyjnej jednego dzierżawcy, 0:59:43](#anchor-image-wtip-min05943)
- [26. Program PowerShell do udostępniania i katalogowania, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * Z Najemców Rozszerzony, 1:03:30](#anchor-image-wtip-min10330)
- [28. Zarządzanie nieprzewidywalnymi obciążeniami najemców, 1:04:36](#anchor-image-wtip-min10436)
- [29. Monitorowanie basenu elastycznego, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generowanie obciążenia i monitorowanie wydajności, 1:09:42](#anchor-image-wtip-min10942)
- [31. Zarządzanie schematem w skali, 1:10:33](#anchor-image-wtip-min11033)
- [32. Kwerenda rozproszona w bazach danych dzierżawy, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demo generowania biletów, 1:12:32](#anchor-image-wtip-min11232)
- [34. Analiza doraźna SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. Wyodrębnianie danych dzierżawy do SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Wykres dziennego rozkładu sprzedaży, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zawiń i wezwanie do działania, 1:19:52](#anchor-image-wtip-min11952)
- [38. Zasoby, aby uzyskać więcej informacji, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Lokalizacje czasu indeksu z adnotacjami w filmie

Kliknięcie dowolnego obrazu zrzutu ekranu prowadzi do dokładnej lokalizacji czasu w filmie.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Zjeżdżalnia powitalna, 0:00:01

*Uczenie się z MYOB: Wzorce projektowania aplikacji SaaS w bazie danych SQL Azure — BRK3120*

[![Slajd powitalny][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Tytuł: Uczenie się z MYOB: Wzorce projektowe dla aplikacji SaaS w bazie danych SQL azure
- Bill.Gibson@microsoft.com
- Główny menedżer programów, baza danych SQL platformy Azure
- Sesja Microsoft Ignite BRK3120, Orlando, FL USA, październik/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Cele sesji, 0:01:53
[![Cele sesji][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatywne modele dla aplikacji z wieloma dzierżawami, z zaletami i wadami.
- Wzorce SaaS w celu zmniejszenia kosztów rozwoju, zarządzania i zasobów.
- Przykładowa aplikacja + skrypty.
- Funkcje PaaS + wzorce SaaS sprawiają, że baza danych SQL jest wysoce skalowalną i ekonomiczną platformą danych dla wielodostępnych usług SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Porządek obrad, 0:04:09
[![Porządku obrad][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Wielodostępna aplikacja internetowa, 0:05:00
[![Aplikacja Wingtip SaaS: wielodostępna aplikacja internetowa][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formularz internetowy aplikacji w akcji, 0:05:39
[![Formularz sieci Web aplikacji w akcji][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Koszt na dzierżawcę (skala, izolacja, odzyskiwanie), 0:06:58
[![Koszt dzierżawcy, skala, izolacja, odzyskiwanie][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Modele baz danych dla wielu dzierżawców: plusy i minusy, 0:09:52
[![Modele baz danych dla wielu dzierżawców: plusy i minusy][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Model hybrydowy łączy zalety MT/ST, 0:12:29
[![Model hybrydowy łączy zalety MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Single-tenant vs multi-tenant: plusy i minusy, 0:13:11
[![Single-tenant vs multi-tenant: plusy i minusy][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pule są opłacalne dla nieprzewidywalnych obciążeń, 0:17:49
[![Pule są opłacalne w przypadku nieprzewidywalnych obciążeń][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Prezentacja bazy danych na dzierżawcę i hybrydowy ST/MT, 0:19:59
[![Demonstracja bazy danych na dzierżawcę i hybrydowy ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Formularz aplikacji na żywo z dojo, 0:20:10
[![Formularz aplikacji na żywo z dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB i nie DBA w zasięgu wzroku, 0:25:06
[![MYOB a nie DBA w zasięgu wzroku][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Przykład użycia basenu elastycznego MYOB, 0:29:30
[![Przykład użycia puli elastycznej MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Nauka od MYOB i innych dostawców oprogramowania, 0:31:25
[![Uczenie się od MYOB i innych dostawców oprogramowania][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Wzory komponują się do scenariusza E2E SaaS, 0:31:42
[![Wzorce komponują się do scenariusza E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical hybrydowy wielodostępny aplikacji SaaS, 0:46:04
[![Canonical hybrydowa wielodostępna aplikacja SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Przykładowa aplikacja Wingtip SaaS, 0:48:01
[![Przykładowa aplikacja Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenariusze i wzorce zbadane w samouczkach, 0:49:00
[![Scenariusze i wzorce zbadane w samouczkach][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Pokaz samouczków i repozytorium GitHub, 0:50:12
[![Samouczki demonstracyjne i repozytorium GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Repozytorium GitHub Microsoft/WingtipSaaS, 0:50:32
[![Repozytorium GitHub Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Odkrywanie wzorców, 0:56:15
[![Eksplorowanie wzorców][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Inicjowanie obsługi administracyjnej najemców i dołączanie, 0:56:19
[![Inicjowanie obsługi administracyjnej dzierżawców i dołączanie][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Udostępnianie obsługi administracyjnej dzierżawców i połączenie aplikacji, 0:57:52
[![Inicjowanie obsługi administracyjnej dzierżawców i połączenia aplikacji][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demo skryptów zarządzania inicjowania obsługi administracyjnej jednego dzierżawy, 0:59:36
[![Pokazy skryptów zarządzania inicjowania obsługi administracyjnej pojedynczej dzierżawy][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Program PowerShell do udostępniania i katalogowania, 0:59:56
[![Program PowerShell do aprowizowania i katalogowania][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * Z Najemców Rozszerzony, 1:03:25
[![T-SQL SELECT * Z Rozszerzonych dzierżawców][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Zarządzanie nieprzewidywalnymi obciążeniami najemców, 1:03:34
[![Zarządzanie nieprzewidywalnymi obciążeniami dzierżawców][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Monitorowanie basenu elastycznego, 1:06:32
[![Elastyczne monitorowanie basenu][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generowanie obciążenia i monitorowanie wydajności, 1:09:37
[![Generowanie obciążenia i monitorowanie wydajności][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Zarządzanie schematem w skali, 1:09:40
[![Zarządzanie schematami na dużą skalę][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Kwerenda rozproszona w bazach danych dzierżawy, 1:11:18
[![Kwerenda rozproszona w bazach danych dzierżawy][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demo generowania biletów, 1:12:28
[![Demo generowania biletów][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Analiza doraźna SSMS, 1:12:35
[![Analiza adokocu SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Wyodrębnianie danych dzierżawy do SQL DW, 1:15:46
[![Wyodrębnianie danych dzierżawy do sql dw][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Wykres dziennego rozkładu sprzedaży, 1:16:38
[![Wykres codziennej dystrybucji sprzedaży][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zawiń i wezwanie do działania, 1:17:43
[![Zawijanie i wezwanie do działania][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Zasoby, aby uzyskać więcej informacji, 1:20:35
[![Zasoby, aby uzyskać więcej informacji][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Wpis na blogu, 22 maja 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepcyjne:* [Wzorce dzierżawy bazy danych SaaS z wieloma dzierżawami][saas-concept-design-patterns-563e]

- *Samouczki:* [Aplikacja Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

- Repozytoria GitHub dla smaków aplikacji najem Wingtip Tickets SaaS:
    - [Repozytorium GitHub dla - Samodzielny model aplikacji][github-wingtip-standaloneapp].
    - [Repozytorium GitHub dla - DB na model dzierżawy][github-wingtip-dbpertenant].
    - [Repozytorium GitHub dla - Model bazy danych z wieloma dzierżawcami][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Następne kroki

- [Pierwszy artykuł w samouczku][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slajd powitalny"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cele sesji."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Porządku obrad."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikacja Wingtip SaaS: wielodostępna aplikacja internetowa"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formularz sieci Web aplikacji w akcji"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Koszt dzierżawcy, skala, izolacja, odzyskiwanie"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modele baz danych dla wielu dzierżawców: plusy i minusy"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Model hybrydowy łączy zalety MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Single-tenant vs multi-tenant: plusy i minusy"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pule są opłacalne w przypadku nieprzewidywalnych obciążeń"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demonstracja bazy danych na dzierżawcę i hybrydowy ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formularz aplikacji na żywo z dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB a nie DBA w zasięgu wzroku"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Przykład użycia puli elastycznej MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Uczenie się od MYOB i innych dostawców oprogramowania"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Wzorce komponują się do scenariusza E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybrydowa wielodostępna aplikacja SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Przykładowa aplikacja Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenariusze i wzorce zbadane w samouczkach"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Pokaz samouczków i repozytorium GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repozytorium GitHub Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Eksplorowanie wzorców"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Inicjowanie obsługi administracyjnej dzierżawców i dołączanie"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Inicjowanie obsługi administracyjnej dzierżawców i połączenia aplikacji"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Pokazy skryptów zarządzania inicjowania obsługi administracyjnej pojedynczej dzierżawy"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Program PowerShell do aprowizowania i katalogowania"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * Z Rozszerzonych dzierżawców"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Zarządzanie nieprzewidywalnymi obciążeniami dzierżawców"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Elastyczne monitorowanie basenu"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generowanie obciążenia i monitorowanie wydajności"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Zarządzanie schematami na dużą skalę"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Kwerenda rozproszona w bazach danych dzierżawy"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo generowania biletów"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analiza adokocu SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Wyodrębnianie danych dzierżawy do sql dw"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Wykres codziennej dystrybucji sprzedaży"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zawijanie i wezwanie do działania"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Zasoby, aby uzyskać więcej informacji"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

