---
title: Indeksowane wideo, aplikacji usługi Azure SQL w usłudze SaaS | Dokumentacja firmy Microsoft
description: W tym artykule indeksy różnych punktach w czasie w naszych 81 minut wideo o projektowaniu dzierżawy SaaS DB, z konferencji Ignite przechowywane 11 października 2017 r. Możesz przejść od razu do części, która Cię interesuje. Co najmniej 3 wzorce są opisane. Opisano funkcje platformy Azure, które upraszczają opracowywanie i zarządzanie nimi.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: bbe220780a3c21e7bfb15d0568904af4ed47f765
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61487283"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Film wideo indeksowane i adnotację dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database

Ten artykuł stanowi adnotacjami indeksu do lokalizacji godziny, minuty 81 wideo o modeli dzierżawy SaaS lub wzorce. Ten artykuł pozwala przejść do tyłu lub do przodu w trakcie filmu wideo do części, która Cię interesuje. Wideo opisano opcje projektu najważniejszych aplikacji wielodostępnej bazie danych w usłudze Azure SQL Database. Film wideo zawiera pokazy, przewodniki kod zarządzania i czasami dokładniej wspieranemu przez środowisko, nie może znajdować się w naszej dokumentacji na piśmie.

Film wideo, zwiększa się informacji w naszej dokumentacji napisane znaleźć pod adresem: 
- *Ogólne:* [Wielodostępne SaaS baza danych dzierżawy wzorców][saas-concept-design-patterns-563e]
- *Samouczki:* [Aplikacja SaaS o nazwie Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

Wideo i artykułów opisują wiele etapy tworzenia aplikacji z wieloma dzierżawami w usłudze Azure SQL Database w chmurze. Specjalne funkcje usługi Azure SQL Database można ułatwić opracowanie i wdrożenie wielodostępne aplikacje, które są łatwiejsze w zarządzaniu i niezawodnie wydajne.

Firma Microsoft regularnie aktualizuje naszej dokumentacji na piśmie. Wideo nie jest edytowany lub aktualizowane, więc po pewnym czasie więcej jego szczegółów może stać się nieaktualne.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekwencja 38 zrzuty ekranu, które są indeksowane w czasie

W tej sekcji indeksuje lokalizację czasu dla 38 dyskusji w ciągu minuty kończą 81 wideo. Każdy indeks czasu jest oznaczona za pomocą zrzut ekranu wideo i czasami z dodatkowymi informacjami.

Każdy indeks czasu jest w formacie *ss*. Na przykład drugi indeksowane miejsca w czasie, etykietą **celów sesji**, rozpoczyna się od lokalizacji przybliżony czas **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compact łącza do lokalizacji wideo indeksowanych czasu

Poniższe sekcje są łącza do ich odpowiednimi sekcjami adnotacjami w dalszej części tego artykułu:

- [1. **(Ponowne uruchomienie)**  Powitalnej slajdów, 0:00:03](#anchor-image-wtip-min00001)
- [2. Cele sesji, 0:03:11](#anchor-image-wtip-min00311)
- [3. Plan, 0:04:17](#anchor-image-wtip-min00417)
- [4. Aplikacja sieci web z wieloma dzierżawami, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formularz sieci web App w działaniu, 0:05:55](#anchor-image-wtip-min00555)
- [6. Koszt (skali, izolacji odzyskiwania), 0 dzierżawy: 09:31](#anchor-image-wtip-min00931)
- [7. Baza danych modeli dla wielu dzierżawców: zalety i wady, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybrydowego modelu łączy korzyści MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Wielodostępne vs jedną dzierżawą: zalety i wady, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pule są ekonomiczne w przypadku nieprzewidywalnych obciążeń 0:19:36](#anchor-image-wtip-min01936)
- [11. Pokaz usługi bazy danych dla dzierżawcy i hybrydowe ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Na żywo z aplikacji, przedstawiający Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. Firma MYOB i nie DBA w kontakt, 0:28:54](#anchor-image-wtip-min02854)
- [14. Przykład użycia puli elastycznej firma MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Uczysz się na podstawie innych niezależnych dostawców oprogramowania, 0 i firma MYOB: 31:36](#anchor-image-wtip-min03136)
- [16. Wzorce compose do scenariusza rozwiązania SaaS E2E, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical hybrydowego wielodostępną aplikację SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. Aplikacji przykładowej aplikacji Wingtip SaaS 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenariuszy i wzorców przedstawione w samouczkach, 0:49:10](#anchor-image-wtip-min04910)
- [20. Pokaz samouczków i repozytorium GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. Repozytorium GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Poznawanie wzorców, 0:56:20](#anchor-image-wtip-min05620)
- [23. Inicjowanie obsługi administracyjnej dzierżaw i dołączania, 0:57:44](#anchor-image-wtip-min05744)
- [24. Inicjowanie obsługi administracyjnej dzierżaw i aplikacji, połączeń, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demonstracja zarządzania skrypty, aprowizacja jednej dzierżawy 0:59:43](#anchor-image-wtip-min05943)
- [26. Program PowerShell w celu aprowizacji i wykazu, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * z TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Zarządzanie obciążeniami dzierżawy nieprzewidywalne, 1:04:36](#anchor-image-wtip-min10436)
- [29. Pula elastyczna, monitorowania, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generowanie obciążenia i monitorowanie wydajności, 1:09:42](#anchor-image-wtip-min10942)
- [31. Zarządzanie schematami na dużą skalę, 1:10:33](#anchor-image-wtip-min11033)
- [32. Zapytanie rozproszone w bazach danych dzierżawy, 1:12:21](#anchor-image-wtip-min11221)
- [33. Pokaz Generowanie biletów, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. Wyodrębnianie danych dzierżawy do usługi SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Wykres rozkładu codzienne sprzedaży, 1:16:48](#anchor-image-wtip-min11648)
- [37. Podsumowanie i wezwań do działania, 1:19:52](#anchor-image-wtip-min11952)
- [38. Zasoby, aby uzyskać więcej informacji, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Lokalizacje czasu adnotacjami indeksu w filmie wideo

Kliknięcie dowolnego obrazu zrzutu ekranu spowoduje przejście do lokalizacji dokładny czas w trakcie filmu wideo.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Ponowne uruchomienie)*  Powitalnej slajdów, 0:00:01

*Uczysz się na podstawie firma MYOB: Wzorce projektowe dla aplikacji SaaS w usłudze Azure SQL Database — BRK3120*

[![Slajd-Zapraszamy!][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Tytuł: Uczysz się na podstawie firma MYOB: Wzorce projektowe dla aplikacji SaaS w usłudze Azure SQL Database
- Bill.Gibson@microsoft.com
- Główny menedżer programu usługi Azure SQL Database
- Microsoft Ignite session BRK3120, Orlando, FL USA, October/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Cele sesji, 0:01:53
[![Cele sesji][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatywne modele aplikacje z wieloma dzierżawami, z zalet i wad.
- Wzorce SaaS, aby ograniczyć koszty rozwoju, zarządzania i zasobów.
- Przykładową aplikację i skrypty.
- Funkcje PaaS i SaaS wzorców tworzenie bazy danych SQL platformy danych o wysokim stopniu skalowalności, wydajne dla wielodostępnej SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Plan, 0:04:09
[![Plan][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Aplikacja sieci web z wieloma dzierżawami, 0:05:00
[![Aplikacja SaaS o nazwie Wingtip: Aplikacja sieci web z wieloma dzierżawcami][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formularz sieci web App w działaniu, 0:05:39
[![Formularz sieci web App w działaniu][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Koszt (skali, izolacji odzyskiwania), 0 dzierżawy: 06:58
[![Dla dzierżawcy kosztów, skala, izolacja, odzyskiwanie][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Baza danych modeli dla wielu dzierżawców: zalety i wady, 0:09:52
[![Baza danych modeli dla wielu dzierżawców: zalety i wady][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybrydowego modelu łączy korzyści MT/ST, 0:12:29
[![Hybrydowego modelu łączy korzyści MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Wielodostępne vs jedną dzierżawą: zalety i wady, 0:13:11
[![Wielodostępne vs jedną dzierżawą: zalety i wady][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pule są ekonomiczne w przypadku nieprzewidywalnych obciążeń 0:17:49
[![Pule są ekonomiczne w przypadku nieprzewidywalnych obciążeń][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Pokaz usługi bazy danych dla dzierżawcy i hybrydowe ST/MT, 0:19:59
[![Pokaz usługi bazy danych dla dzierżawcy i hybrydowe ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Na żywo z aplikacji, przedstawiający Dojo, 0:20:10
[![Formularz działającej aplikacji, przedstawiający Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. Firma MYOB i nie DBA w kontakt, 0:25:06
[![Firma MYOB i nie DBA toczyła się][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Przykład użycia puli elastycznej firma MYOB, 0:29:30
[![Przykład użycia usługi MYOB puli elastycznej][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Uczysz się na podstawie innych niezależnych dostawców oprogramowania, 0 i firma MYOB: 31:25
[![Uczysz się na podstawie innych niezależnych dostawców oprogramowania i firma MYOB][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Wzorce compose do scenariusza rozwiązania SaaS E2E, 0:31:42
[![Wzorce compose do scenariusza rozwiązania SaaS E2E][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical hybrydowego wielodostępną aplikację SaaS, 0:46:04
[![Canonical hybrydowego wielodostępną aplikację SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Aplikacji przykładowej aplikacji Wingtip SaaS 0:48:01
[![Przykładową aplikację w bazie wiedzy o nazwie Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenariuszy i wzorców przedstawione w samouczkach, 0:49:00
[![Przedstawione w samouczkach wzorców i scenariuszy][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Pokaz samouczków i repozytorium GitHub, 0:50:12
[![W samouczkach pokaz i repozytorium GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Repozytorium GitHub Microsoft/WingtipSaaS, 0:50:32
[![Repozytorium GitHub Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Poznawanie wzorców, 0:56:15
[![Poznawanie wzorców][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Inicjowanie obsługi administracyjnej dzierżaw i dołączania, 0:56:19
[![Inicjowanie obsługi administracyjnej dzierżaw i dołączania][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Inicjowanie obsługi administracyjnej dzierżaw i aplikacji, połączeń, 0:57:52
[![Inicjowanie obsługi administracyjnej dzierżaw i połączenie aplikacji][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demonstracja zarządzania skrypty, aprowizacja jednej dzierżawy 0:59:36
[![Pokaz skrypty zarządzania aprowizacja jednej dzierżawy][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Program PowerShell w celu aprowizacji i wykazu, 0:59:56
[![Program PowerShell w celu aprowizacji i wykazu][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * z TenantsExtended, 1:03:25
[![T-SQL SELECT * z TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Zarządzanie obciążeniami dzierżawy nieprzewidywalne, 1:03:34
[![Zarządzanie obciążeniami dzierżawy nieprzewidywalne][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Pula elastyczna, monitorowania, 1:06:32
[![Monitorowanie pul elastycznych][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generowanie obciążenia i monitorowanie wydajności, 1:09:37
[![Generowanie obciążenia i monitorowanie wydajności][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Zarządzanie schematami na dużą skalę, 1:09:40
[![Zarządzanie schematami na dużą skalę][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Zapytanie rozproszone w bazach danych dzierżawy, 1:11:18
[![Zapytanie rozproszone w bazach danych dzierżawy][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Pokaz Generowanie biletów, 1:12:28
[![Pokaz Generowanie biletów][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analytics, 1:12:35
[![SSMS adhoc analytics][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Wyodrębnianie danych dzierżawy do usługi SQL DW, 1:15:46
[![Wyodrębnianie danych dzierżawy do usługi SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Wykres rozkładu codzienne sprzedaży, 1:16:38
[![Wykres rozkładu codzienne sprzedaży][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Podsumowanie i wezwań do działania, 1:17:43
[![Podsumowanie i wywołanie akcji][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Zasoby, aby uzyskać więcej informacji, 1:20:35
[![Zasoby, aby uzyskać więcej informacji][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Wpis w blogu 22 maja 2017 r.][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Ogólne:* [Wielodostępne SaaS baza danych dzierżawy wzorców][saas-concept-design-patterns-563e]

- *Samouczki:* [Aplikacja SaaS o nazwie Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

- Repozytoria GitHub dla odmian aplikacji SaaS o nazwie Wingtip Tickets dzierżawy:
    - [Repozytorium GitHub dla — model aplikacji autonomicznej][github-wingtip-standaloneapp].
    - [Repozytorium GitHub dla — model bazy danych dla dzierżawy][github-wingtip-dbpertenant].
    - [Repozytorium GitHub dla — model bazy danych z wieloma dzierżawami][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Kolejne kroki

- [Pierwszy artykuł zawierający samouczek][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slajd-Zapraszamy!"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cele sesji."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Plan."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikacja SaaS o nazwie Wingtip: Aplikacja sieci web z wieloma dzierżawcami"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formularz sieci web App w działaniu"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Dla dzierżawcy kosztów, skala, izolacja, odzyskiwanie"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Baza danych modeli dla wielu dzierżawców: zalety i wady"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybrydowego modelu łączy korzyści MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Wielodostępne vs jedną dzierżawą: zalety i wady"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pule są ekonomiczne w przypadku nieprzewidywalnych obciążeń"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Pokaz usługi bazy danych dla dzierżawcy i hybrydowe ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formularz działającej aplikacji, przedstawiający Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "Firma MYOB i nie DBA toczyła się"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Przykład użycia usługi MYOB puli elastycznej"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Uczysz się na podstawie innych niezależnych dostawców oprogramowania i firma MYOB"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Wzorce compose do scenariusza rozwiązania SaaS E2E"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybrydowego wielodostępną aplikację SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Przykładową aplikację w bazie wiedzy o nazwie Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Przedstawione w samouczkach wzorców i scenariuszy"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Pokaz samouczków i repozytorium GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repozytorium GitHub Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Poznawanie wzorców"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Inicjowanie obsługi administracyjnej dzierżaw i dołączania"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Inicjowanie obsługi administracyjnej dzierżaw i połączenie aplikacji"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Pokaz skrypty zarządzania aprowizacja jednej dzierżawy"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Program PowerShell w celu aprowizacji i wykazu"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * z TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Zarządzanie obciążeniami dzierżawy nieprzewidywalne"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorowanie pul elastycznych"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generowanie obciążenia i monitorowanie wydajności"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Zarządzanie schematami na dużą skalę"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Zapytanie rozproszone w bazach danych dzierżawy"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Pokaz Generowanie biletów"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analizy ad hoc w programie SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Wyodrębnianie danych dzierżawy do usługi SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Wykres rozkładu codzienne sprzedaży"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Podsumowanie i wywołanie akcji"

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

