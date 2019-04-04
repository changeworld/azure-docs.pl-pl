---
title: Tworzenie i przywracanie kopii zapasowej w usłudze BizTalk Services | Dokumentacja firmy Microsoft
description: Usługa BizTalk Services zawiera kopii zapasowej i przywracania. Informacje o sposobie tworzenia i przywracania kopii zapasowej i ustalić, kopiami zapasowymi. MABS, WABS
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
ms.openlocfilehash: ee86b9aa2d920668cf036f3e8f8634e9289e8913
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916873"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Wykonywanie kopii zapasowych i przywracanie

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Usługa Azure BizTalk Services obejmuje funkcje tworzenia kopii zapasowych i przywracania. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Połączenia hybrydowe są nie kopię zapasową, niezależnie od wersji. Należy ponownie utworzyć połączenia hybrydowego.


## <a name="before-you-begin"></a>Przed rozpoczęciem
* Kopia zapasowa i przywracanie nie mogą być dostępne dla wszystkich wersji. See [BizTalk Services: Wykres przedstawiający wersje](biztalk-editions-feature-chart.md).
* Zawartość kopii zapasowej można przywrócić do tej samej usługi BizTalk lub nową usługę BizTalk. Aby przywrócić usługę BizTalk, przy użyciu takiej samej nazwie, należy usunąć istniejące usługi BizTalk, a nazwa musi być dostępna. Po usunięciu usługi BizTalk może potrwać dłużej, niż dla tej samej nazwie, które mają być dostępne. Jeśli nie możesz czekać na tej samej nazwie, która była dostępna, Przywróć do nowej usługi BizTalk.
* Usługa BizTalk Services można przywrócić do tej samej wersji lub nowszej wersji. Przywracanie starszej wersji usługi BizTalk Services, od kiedy wykonano kopię zapasową, nie jest obsługiwana.
  
    Na przykład można przywrócić kopię zapasową przy użyciu wersji podstawowa do wersji Premium. Nie można przywrócić kopię zapasową przy użyciu wersji Premium do wersji standardowa.
* Aby utrzymać ciągłość numerów kontrolnych zapasową numerów kontrolnych EDI. Jeśli komunikaty są przetwarzane po utworzeniu ostatniej kopii zapasowej, przywrócenie tej kopii zapasowej zawartości może spowodować numerów kontrolnych duplikatów.
* W przypadku usługi batch ma aktywne wiadomości, proces partii **przed** tworzona kopia zapasowa. Podczas tworzenia kopii zapasowej (jako wymagane lub zaplanowane), nigdy nie są przechowywane komunikaty w partii. 
  
    **Jeśli kopia zapasowa pochodzi z aktywne komunikaty w partii, te komunikaty są nie kopię zapasową, a w związku z tym zostaną utracone.**
* Opcjonalnie: W portalu usługi BizTalk Services należy zatrzymać wszystkie operacje zarządzania.

## <a name="create-a-backup"></a>Tworzenie kopii zapasowej
Kopię zapasową można wykonać w dowolnym momencie i jest całkowicie kontrolowana przez użytkownika. Aby utworzyć kopię zapasową, użyj [REST API for Managing BizTalk Services na platformie Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

## <a name="restore"></a>Przywracanie
Aby przywrócić kopię zapasową, użyj [REST API for Managing BizTalk Services na platformie Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

### <a name="postrestore"></a>Po przywróceniu kopii zapasowej
Usługa BizTalk zawsze zostanie przywrócony w **zawieszone** stanu. W tym stanie może wprowadzać żadnych zmian konfiguracji, zanim nowe środowisko będzie działać, w tym:

* Jeśli utworzono aplikacje usługi BizTalk przy użyciu zestawu SDK usługi Azure BizTalk Services, może być konieczne zaktualizowanie poświadczeń kontroli dostępu (ACS) w tych aplikacji, aby pracować z przywróconej środowiska.
* Możesz przywrócić usługi BizTalk do replikowania istniejące środowisko usługi BizTalk. W takiej sytuacji w przypadku umów skonfigurowane w portalu usługi BizTalk Services, oryginalnym, korzystających z folderu źródłowego FTP, konieczne może być Aktualizacja umów w środowisku nowo przywrócony do korzystania z folderu FTP innego źródła. W przeciwnym razie może być dwóch różnych umów próby ściągania tego samego komunikatu.
* Jeśli przywrócono korzystanie z wielu środowisk usługi BizTalk, upewnij się, że platformą docelową jest program odpowiednie środowisko w aplikacjach Visual Studio, poleceń cmdlet programu PowerShell, interfejsy API REST lub handlowymi partnera OM interfejsów API zarządzania.
* Jest dobrą praktyką jest Konfigurowanie automatycznych kopii zapasowych na nowo przywrócony środowiska usługi BizTalk.

## <a name="what-gets-backed-up"></a>Kopiami zapasowymi
Po utworzeniu kopii zapasowej tworzy kopię zapasową następujących elementów:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementy kopii zapasowej</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portalu usługi Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>Konfiguracja i środowiska uruchomieniowego</td> 
<td>
<ul>
<li>Szczegóły dotyczące partnera i profilu</li>
<li>Umów z partnerami</li>
<li>Niestandardowe zestawy wdrożony</li>
<li>Mostki wdrożony</li>
<li>Certyfikaty</li>
<li>Przekształcenia wdrożony</li>
<li>Potoki</li>
<li>Szablony utworzone i zapisane w portalu usługi BizTalk Services</li>
<li>X12 mapowania ST01 i GS01</li>
<li>Numery kontrolne (EDI)</li>
<li>Wartości MIC komunikat AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</tr> 
<tr>
<td>Certyfikat SSL</td> 
<td>
<ul>
<li>Dane dotyczące certyfikatu SSL</li>
<li>Hasło certyfikatu protokołu SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk Service Settings</td> 
<td>
<ul>
<li>Liczba jednostek skali</li>
<li>Wersja</li>
<li>Wersja produktu</li>
<li>Region/centrum danych</li>
<li>Obszaru nazw usługi Access Control Service (ACS) i klucz</li>
<li>Parametry połączenia bazy danych śledzenia</li>
<li>Podane parametry połączenia konta magazynu</li>
<li>Monitorowanie parametry połączenia konta magazynu</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Dodatkowe elementy</strong></td>
</tr> 
<tr>
<td>Baza danych śledzenia</td> 
<td>Po utworzeniu usługi BizTalk, są wprowadzane Szczegóły śledzenia bazą danych, łącznie z serwera bazy danych SQL Azure i śledzenie nazwę bazy danych. Baza danych śledzenia nie jest automatycznie kopii zapasowej.
<br/><br/>
<strong>Ważne</strong><br/>
Jeśli baza danych śledzenia zostanie usunięty, a następnie odzyskać na potrzeby bazy danych, musi istnieć poprzedniej kopii zapasowej. Jeśli tworzenie kopii zapasowej nie istnieje, baza danych śledzenia i jego dane nie są możliwe do odzyskania. W takiej sytuacji należy utworzyć nową bazę danych śledzenia o takiej samej nazwie bazy danych. Replikacja geograficzna jest zalecane.</td>
</tr> 
</table>

## <a name="next"></a>Następne kroki
Aby utworzyć usługi Azure BizTalk Services, przejdź do [usługi BizTalk Services: Inicjowanie obsługi administracyjnej](https://go.microsoft.com/fwlink/p/?LinkID=302280). Aby rozpocząć tworzenie aplikacji, przejdź do artykułu [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197) (Usługa Azure BizTalk Services).

## <a name="see-also"></a>Zobacz też
* [Usługa BizTalk kopii zapasowej](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Przywracanie usługi BizTalk z kopii zapasowej](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: Inicjowanie obsługi](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Wykres stanu aprowizacji](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Karty Pulpit nawigacyjny, Monitor i Skala](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Ograniczanie przepływności](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Nazwa wystawcy i klucz wydawcy](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

