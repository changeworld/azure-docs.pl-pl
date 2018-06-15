---
title: Tworzenie i przywracanie kopii zapasowej w usługach BizTalk | Dokumentacja firmy Microsoft
description: Usługi BizTalk Services zawiera kopii zapasowej i przywracania. Informacje o sposobie tworzenia i przywracania kopii zapasowej, aby ustalić kopiami zapasowymi. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102345"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Usługi BizTalk Azure obejmuje możliwości tworzenia kopii zapasowych i przywracania. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Połączenia hybrydowe nie kopii zapasowej, niezależnie od wersji. Należy ponownie utworzyć połączeń hybrydowych.


## <a name="before-you-begin"></a>Przed rozpoczęciem
* Kopii zapasowej i przywracania może nie być dostępne dla wszystkich wersji. Zobacz [usługi BizTalk Services: wersje wykresu](biztalk-editions-feature-chart.md).
* Zawartość kopii zapasowej można przywrócić do tej samej usługi BizTalk lub nową usługę BizTalk. Aby przywrócić usługi BizTalk, przy użyciu takiej samej nazwie, należy usunąć istniejącej usługi BizTalk i nazwa musi być dostępna. Po usunięciu usługi BizTalk, może potrwać dłużej, niż żądana dla tej samej nazwie, które mają być dostępne. Jeśli użytkownik nie może czekać na tej samej nazwie, które mają być dostępne, następnie przywróć nową usługę BizTalk.
* Usługi BizTalk Services można przywrócić do tej samej wersji lub nowszą wersję. Przywracanie usługi BizTalk Services do niższej wersji, z po wykonaniu kopii zapasowej, nie jest obsługiwane.
  
    Na przykład można przywrócić kopii zapasowej przy użyciu podstawowych wydanie do wersji Premium. Nie można przywrócić kopii zapasowej przy użyciu wersji Premium do Standard Edition.
* Numery kontroli EDI kopię zapasową do kontynuacji numery kontroli. Wiadomości są przetwarzane po utworzeniu ostatniej kopii zapasowej, przywrócenia tej kopii zapasowej zawartości może spowodować numery zduplikowane kontroli.
* Partia ma aktywne wiadomości, proces partii **przed** wykonywania kopii zapasowej. Podczas tworzenia kopii zapasowej (jako wymagane lub zaplanowane), wiadomości w partiach nigdy nie są przechowywane. 
  
    **Jeśli wykonywana jest kopia zapasowa z active wiadomości w partii, te komunikaty nie kopii zapasowej i w związku z tym zostaną utracone.**
* Opcjonalnie: W portalu usługi BizTalk, Zatrzymaj wszystkie operacje zarządzania.

## <a name="create-a-backup"></a>Tworzenie kopii zapasowej
Kopię zapasową można wykonać w dowolnym momencie i całkowicie jest kontrolowany przez użytkownika. Aby utworzyć kopię zapasową, użyj [interfejsu API REST dla usługi BizTalk Services zarządzania na platformie Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Przywracanie
Aby przywrócić kopię zapasową, użyj [interfejsu API REST dla usługi BizTalk Services zarządzania na platformie Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Po przywróceniu kopii zapasowej
Usługi BizTalk zawsze jest przywracany **zawieszone** stanu. W tym stanie można wprowadzać żadnych zmian konfiguracji, zanim nowe środowisko będzie działać, w tym:

* Jeśli utworzono usługę BizTalk aplikacji przy użyciu zestawu SDK usługi Azure BizTalk, może być konieczne można zaktualizować poświadczeń kontroli dostępu (ACS) w tych aplikacji, aby pracować z przywróconą środowiska.
* Możesz przywrócić usługi BizTalk do replikowania istniejącego środowiska usługi BizTalk. W takiej sytuacji, jeśli są skonfigurowane w portalu usługi BizTalk Services oryginalnej Umowy, korzystających z folderu źródłowego FTP, może być konieczne Aktualizacja umów w środowisku nowo przywrócony do korzystania z folderu FTP innego źródła. W przeciwnym razie może być dwóch różnych umów próby pobierać ten sam komunikat.
* Jeśli przywracany mają wiele środowisk usługi BizTalk, upewnij się, że poprawne środowisko w aplikacji Visual Studio, poleceń cmdlet programu PowerShell, interfejsów API REST lub handlem partnera OM interfejsów API Management są przeznaczone.
* Jest dobrym rozwiązaniem, aby skonfigurować automatyczne tworzenie kopii zapasowych na nowo przywrócony środowiska usługi BizTalk.

## <a name="what-gets-backed-up"></a>Kopiami zapasowymi
Po utworzeniu kopii zapasowej, tworzy kopię zapasową następujących elementów:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementy kopii zapasowej</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal usług Azure BizTalk</strong></td>
</tr> 
<tr>
<td>Konfiguracja i środowiska wykonawczego</td> 
<td>
<ul>
<li>Szczegóły partnera i profilu</li>
<li>Umowy z partnerami</li>
<li>Niestandardowe zestawy wdrożony</li>
<li>Mostków wdrożony</li>
<li>Certyfikaty</li>
<li>Transformacje wdrożony</li>
<li>Potoki</li>
<li>Szablony utworzony i zapisany w portalu usługi BizTalk</li>
<li>X12 mapowania ST01 i GS01</li>
<li>Numery kontroli (EDI)</li>
<li>Wartości AS2 komunikat Micznych</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Usługi BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certyfikat SSL</td> 
<td>
<ul>
<li>Dane certyfikatu SSL</li>
<li>Hasło certyfikatu SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Ustawienia usługi BizTalk</td> 
<td>
<ul>
<li>Liczba jednostek skali</li>
<li>Wersja</li>
<li>Wersja produktu:</li>
<li>Region/centrum danych</li>
<li>Dostęp do usługi kontroli (ACS) w przestrzeni nazw i klucza</li>
<li>Parametry połączenia bazy danych śledzenia</li>
<li>Archiwizowanie parametry połączenia konta magazynu</li>
<li>Monitorowanie parametrów połączenia konta magazynu</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Dodatkowe elementy</strong></td>
</tr> 
<tr>
<td>Śledzenie bazy danych</td> 
<td>Po utworzeniu usługi BizTalk, Szczegóły śledzenia bazy danych zostały wprowadzone, łącznie z serwerem bazy danych SQL Azure i Nazwa śledzenia bazy danych. Baza danych śledzenia nie jest automatycznie kopii zapasowej.
<br/><br/>
<strong>Ważne</strong><br/>
Jeśli baza danych śledzenia zostaje usunięta i odzyskać na potrzeby bazy danych, musi istnieć poprzedniej kopii zapasowej. Jeśli kopia zapasowa nie istnieje, bazy danych śledzenia i jego dane nie są możliwe do odzyskania. W takiej sytuacji należy utworzyć nową bazę danych śledzenia o takiej samej nazwie bazy danych. Replikacja geograficzna jest zalecane.</td>
</tr> 
</table>

## <a name="next"></a>Następne kroki
Aby utworzyć usługi BizTalk Azure, przejdź do [usługi BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280). Aby rozpocząć tworzenie aplikacji, przejdź do artykułu [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197) (Usługa Azure BizTalk Services).

## <a name="see-also"></a>Zobacz też
* [Usługi BizTalk kopii zapasowej](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Przywracanie z kopii zapasowej usługi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Usługi BizTalk Services: Developer, podstawowa, standardowa i Premium Edition wykresu](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Usługi BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

