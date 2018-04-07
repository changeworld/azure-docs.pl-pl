---
title: Indeksowane wideo, aplikacja Azure SaaS SQL | Dokumentacja firmy Microsoft
description: W tym artykule indeksuje różnych punktach w czasie w naszym 81 minut wideo dotyczących projektu aplikacji dzierżawy SaaS bazy danych, z konferencji Ignite przechowywać 11 października 2017 r. Możesz przejść od razu do części interesujący Cię. Wzorce co najmniej 3 opisano. Opisano funkcje platformy Azure, które upraszczają proces projektowania i zarządzania nimi.
services: sql-database
ms.date: 04/01/2018
ms.service: sql-database
ms.reviewer: billgib
ms.topic: article
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 7dcd4681833f227f32534ae3053b7b482bb813bc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="video-indexed-and-annotated-for-mulit-tenant-saas-app-using-azure-sql-database"></a>Wideo indeksowane i opatrzone adnotacjami dla aplikacji SaaS mulit dzierżawcy przy użyciu bazy danych SQL Azure

W tym artykule jest indeks adnotacjami w lokalizacji czasu protokołu 81 wideo o SaaS dzierżawy modele lub wzorce. W tym artykule można przejść do tyłu lub do przodu w wideo, do którego części można omawiać. Wideo opisano opcje głównych projektowania aplikacji wielodostępnych bazy danych w bazie danych SQL Azure. Wideo zawiera pokazy, wskazówki dotyczące zarządzania kodu i czasem szczegółowo poinformowany przez środowisko nie może znajdować się w naszej dokumentacji napisane.

Wideo amplifies informacji w naszej dokumentacji napisane znaleźć pod adresem: 
- *Koncepcyjnych:* [SaaS wielodostępne bazy danych wzorce dzierżawy][saas-concept-design-patterns-563e]
- *Samouczki:* [aplikacji SaaS biletów Wingtip][saas-how-welcome-wingtip-app-679t]

Wideo i artykułów opisują wiele etapy tworzenia aplikacji wielodostępne w bazie danych SQL Azure w chmurze. Specjalne funkcje bazy danych SQL Azure można ułatwić opracowanie i wdrożenie wielodostępne aplikacje, które są łatwiejsze w zarządzaniu i niezawodną wydajność.

Firma Microsoft regularnie aktualizować naszej dokumentacji napisane. Wideo nie jest edytowany ani zaktualizowany, tak po pewnym czasie więcej szczegółów, jego może stać się nieaktualne.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekwencja 38 indeksowane czasu zrzuty ekranu

W tej sekcji indeksuje miejsca w czasie do dyskusji 38 przez całą taką minutę 81 wideo. Każdy indeks czasu odnoszący się z zrzut ekranu wideo, a czasami dodatkowe informacje.

Każdy indeks czas jest w formacie *: mm: ss*. Na przykład drugi indeksowane miejsca w czasie, którego etykietą jest **celów sesji**, rozpoczyna się od lokalizacji przybliżony czas **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compact łącza do lokalizacji czasu indeksowanego wideo

Poniższe sekcje są łącza do ich odpowiednich sekcjach adnotacjami w dalszej części tego artykułu:

- [1. **(Uruchom)**  Slajdów-Zapraszamy 0:00:03](#anchor-image-wtip-min00001)
- [2. Celów sesji, 0:03:11](#anchor-image-wtip-min00311)
- [3. Porządek, 0:04:17](#anchor-image-wtip-min00417)
- [4. Aplikacja sieci web z wieloma dzierżawcami, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formularz sieci web aplikacji, w akcji, 0:05:55](#anchor-image-wtip-min00555)
- [6. Koszt (skali, izolacji, odzyskiwania), 0 dzierżawy: 09:31](#anchor-image-wtip-min00931)
- [7. Bazy danych modeli dla wielu dzierżawców: zalet i wad, 0:11:59](#anchor-image-wtip-min01159)
- [8. Model hybrydowego miesza zalet MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Wielodostępne vs pojedynczej dzierżawy: zalet i wad, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pule są ekonomicznego nieprzewidziany w przypadku obciążeń 0:19:36](#anchor-image-wtip-min01936)
- [11. Wersja demonstracyjna dla bazy danych dla dzierżawy i hybrydowych ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Na żywo z aplikacji przedstawiający Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB i nie DBA przewidzieć, 0:28:54](#anchor-image-wtip-min02854)
- [14. Przykład użycia MYOB puli elastycznej, 0:29:40](#anchor-image-wtip-min02940)
- [15. Learning MYOB i innych niezależnym dostawcom oprogramowania 0:31:36](#anchor-image-wtip-min03136)
- [16. Wzorce tworzą w scenariuszu E2E SaaS 0:43:15](#anchor-image-wtip-min04315)
- [17. Aplikacji SaaS wielodostępne Canonical hybrydowych, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS przykładowej aplikacji, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenariusze i wzorce przedstawione w samouczkach, 0:49:10](#anchor-image-wtip-min04910)
- [20. Wersja demonstracyjna samouczki i repozytorium Github, 0:50:18](#anchor-image-wtip-min05018)
- [21. Github repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Eksploracja wzorce 0:56:20](#anchor-image-wtip-min05620)
- [23. Inicjowanie obsługi administracyjnej dzierżawcy i dołączania, 0:57:44](#anchor-image-wtip-min05744)
- [24. Inicjowanie obsługi administracyjnej dzierżaw i aplikacji połączenia, 0:58:58](#anchor-image-wtip-min05858)
- [25. Pokaz zarządzania skrypty inicjowania obsługi administracyjnej pojedynczej dzierżawy 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell, aby udostępnić i katalogu, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * z TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Zarządzanie obciążeń nieprzewidywalne dzierżawy, 1:04:36](#anchor-image-wtip-min10436)
- [29. Pula elastyczna monitorowania, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generowanie obciążenia monitorowania i wydajności, 1:09:42](#anchor-image-wtip-min10942)
- [31. Zarządzanie schematami na dużą skalę, 1:10:33](#anchor-image-wtip-min11033)
- [32. Zapytania rozproszone dla baz danych dzierżawy, 1:12:21](#anchor-image-wtip-min11221)
- [33. Wersja demonstracyjna generowania biletu, 1:12:32](#anchor-image-wtip-min11232)
- [34. Analiza ad hoc SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. Wyodrębnianie danych dzierżawy do magazynu danych SQL, 1:16:32](#anchor-image-wtip-min11632)
- [36. Wykres dystrybucji sprzedaży codziennie, 1:16:48](#anchor-image-wtip-min11648)
- [37. Dobiega końca i wywołanie akcji, 1:19:52](#anchor-image-wtip-min11952)
- [38. Zasoby, aby uzyskać więcej informacji, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Lokalizacje czasu adnotacjami indeksu w wideo

Kliknięcie przycisku żadnego obrazu zrzutu ekranu przejście do lokalizacji dokładny czas wideo.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Uruchom)*  Slajdów-Zapraszamy 0:00:01

*Learning z MYOB: wzorce dla aplikacji SaaS w usłudze Azure SQL Database — BRK3120 projektowe*

[![Slajd-Zapraszamy!][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Nazwa: Wiedzy MYOB: Projektowanie wzorce dla aplikacji SaaS w bazie danych SQL Azure
- Bill.Gibson@microsoft.com
- Główny menedżer programu, baza danych Azure SQL
- Microsoft Ignite session BRK3120, Orlando, FL USA, October/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Celów sesji, 0:01:53
[![Cele sesji][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatywne modeli dla aplikacji z wieloma dzierżawcami, z zalet i wad.
- Wzorce SaaS zmniejszyć koszty rozwoju, zarządzania i zasobów.
- Przykładowa aplikacja + skryptów.
- Funkcje PaaS + wzorce SaaS tworzenie bazy danych SQL platformy danych wysoce skalowalne, ekonomiczne dla wielodostępnych SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Porządek, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Aplikacja sieci web z wieloma dzierżawcami, 0:05:00
[![Aplikacja SaaS Wingtip: aplikacja sieci web z wieloma dzierżawcami][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formularz sieci web aplikacji, w akcji, 0:05:39
[![Formularz sieci web aplikacji, w akcji][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Koszt (skali, izolacji, odzyskiwania), 0 dzierżawy: 06:58
[![Dla dzierżawcy kosztów, skali, izolacji, odzyskiwania][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Bazy danych modeli dla wielu dzierżawców: zalet i wad, 0:09:52
[![Bazy danych modeli dla wielu dzierżawców: zalet i wad][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Model hybrydowego miesza zalet MT/ST, 0:12:29
[![Model hybrydowego miesza zalet MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Wielodostępne vs pojedynczej dzierżawy: zalet i wad, 0:13:11
[![Wielodostępne vs pojedynczej dzierżawy: zalet i wad][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pule są ekonomicznego nieprzewidziany w przypadku obciążeń 0:17:49
[![Pule są ekonomiczny dla obciążeń nieprzewidywalne][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Wersja demonstracyjna dla bazy danych dla dzierżawy i hybrydowych ST/MT, 0:19:59
[![Wersja demonstracyjna dla bazy danych dla dzierżawy i hybrydowych ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Na żywo z aplikacji przedstawiający Dojo, 0:20:10
[![Wyświetlanie Dojo formularza aktywnej aplikacji][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB i nie DBA przewidzieć, 0:25:06
[![MYOB i nie DBA przewidzieć][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Przykład użycia MYOB puli elastycznej, 0:29:30
[![Przykład użycia puli elastycznej MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Learning MYOB i innych niezależnym dostawcom oprogramowania 0:31:25
[![Uczenie z MYOB i innych niezależnych dostawców oprogramowania][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Wzorce tworzą w scenariuszu E2E SaaS 0:31:42
[![Wzorce tworzą w scenariuszu E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Aplikacji SaaS wielodostępne Canonical hybrydowych, 0:46:04
[![Canonical hybrydowego wielodostępne SaaS aplikacji][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS przykładowej aplikacji, 0:48:01
[![Wingtip SaaS przykładowej aplikacji][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenariusze i wzorce przedstawione w samouczkach, 0:49:00
[![Scenariusze i wzorce przedstawione w samouczków][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Wersja demonstracyjna samouczki i repozytorium Github, 0:50:12
[![Wersja demonstracyjna samouczki i repozytorium Github][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Github repo Microsoft/WingtipSaaS, 0:50:32
[![Github repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Eksploracja wzorce 0:56:15
[![Eksploracja wzorce][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Inicjowanie obsługi administracyjnej dzierżawcy i dołączania, 0:56:19
[![Inicjowanie obsługi administracyjnej dzierżawcy i dołączania][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Inicjowanie obsługi administracyjnej dzierżaw i aplikacji połączenia, 0:57:52
[![Inicjowanie obsługi administracyjnej dzierżawcy i połączenie aplikacji][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Pokaz zarządzania skrypty inicjowania obsługi administracyjnej pojedynczej dzierżawy 0:59:36
[![Pokaz inicjowania obsługi administracyjnej pojedynczej dzierżawy skrypty zarządzania][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell, aby udostępnić i katalogu, 0:59:56
[![PowerShell, aby udostępnić i katalogu][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * z TenantsExtended, 1:03:25
[![T-SQL SELECT * z TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Zarządzanie obciążeń nieprzewidywalne dzierżawy, 1:03:34
[![Zarządzanie obciążeń nieprzewidywalne dzierżawy][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Pula elastyczna monitorowania, 1:06:32
[![Monitorowanie puli elastycznej][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generowanie obciążenia monitorowania i wydajności, 1:09:37
[![Generowanie obciążenia i monitorowanie wydajności][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Zarządzanie schematami na dużą skalę, 1:09:40
[![Zarządzanie schematami na dużą skalę][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Zapytania rozproszone dla baz danych dzierżawy, 1:11:18
[![Zapytania rozproszone dla dzierżawy baz danych][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Wersja demonstracyjna generowania biletu, 1:12:28
[![Wersja demonstracyjna generowania biletu][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Analiza ad hoc SSMS, 1:12:35
[![Analiza ad hoc SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Wyodrębnianie danych dzierżawy do magazynu danych SQL, 1:15:46
[![Wyodrębnianie danych dzierżawy do magazynu danych SQL][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Wykres dystrybucji sprzedaży codziennie, 1:16:38
[![Wykres codzienne dystrybucji sprzedaży][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Dobiega końca i wywołanie akcji, 1:17:43
[![Dobiega końca i wywołanie akcji][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Zasoby, aby uzyskać więcej informacji, 1:20:35
[![Zasoby, aby uzyskać więcej informacji][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Wpis w blogu, 22 maja 2017 r.][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepcyjnych:* [SaaS wielodostępne bazy danych wzorce dzierżawy][saas-concept-design-patterns-563e]

- *Samouczki:* [aplikacji SaaS biletów Wingtip][saas-how-welcome-wingtip-app-679t]

- Repozytoriów Github dla odmian aplikacji SaaS biletów Wingtip dzierżawy:
    - [Repozytorium Github dla — model aplikacji autonomicznej][github-wingtip-standaloneapp].
    - [Repozytorium Github dla - DB na dzierżawy modelu][github-wingtip-dbpertenant].
    - [Repozytorium Github dla - DB wielodostępne modelu][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Kolejne kroki

- [Pierwszy samouczek artykułu][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slajd-Zapraszamy!"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cele sesji."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Plan."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikacja SaaS Wingtip: aplikacja sieci web z wieloma dzierżawcami"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formularz sieci web aplikacji, w akcji"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Dla dzierżawcy kosztów, skali, izolacji, odzyskiwania"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Bazy danych modeli dla wielu dzierżawców: zalet i wad"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Model hybrydowego miesza zalet MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Wielodostępne vs pojedynczej dzierżawy: zalet i wad"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pule są ekonomiczny dla obciążeń nieprzewidywalne"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Wersja demonstracyjna dla bazy danych dla dzierżawy i hybrydowych ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Wyświetlanie Dojo formularza aktywnej aplikacji"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB i nie DBA przewidzieć"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Przykład użycia puli elastycznej MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Uczenie z MYOB i innych niezależnych dostawców oprogramowania"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Wzorce tworzą w scenariuszu E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybrydowego wielodostępne SaaS aplikacji"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS przykładowej aplikacji"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenariusze i wzorce przedstawione w samouczków"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Wersja demonstracyjna samouczki i repozytorium Github"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Github repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Eksploracja wzorce"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Inicjowanie obsługi administracyjnej dzierżawcy i dołączania"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Inicjowanie obsługi administracyjnej dzierżawcy i połączenie aplikacji"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Pokaz inicjowania obsługi administracyjnej pojedynczej dzierżawy skrypty zarządzania"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell, aby udostępnić i katalogu"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * z TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Zarządzanie obciążeń nieprzewidywalne dzierżawy"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorowanie puli elastycznej"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generowanie obciążenia i monitorowanie wydajności"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Zarządzanie schematami na dużą skalę"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Zapytania rozproszone dla dzierżawy baz danych"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Wersja demonstracyjna generowania biletu"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analiza ad hoc SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Wyodrębnianie danych dzierżawy do magazynu danych SQL"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Wykres codzienne dystrybucji sprzedaży"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Dobiega końca i wywołanie akcji"

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

