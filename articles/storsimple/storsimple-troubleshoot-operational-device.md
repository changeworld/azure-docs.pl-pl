---
title: Rozwiązywanie problemów z wdrożonym urządzeniem StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób diagnozowania i naprawiania błędów występujących na urządzeniu StorSimple, które jest aktualnie wdrożone i operacyjne.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933293"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Rozwiązywanie problemów z operacyjnym urządzeniem StorSimple
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Omówienie
Ten artykuł zawiera przydatne wskazówki dotyczące rozwiązywania problemów z konfiguracją, które mogą wystąpić po wdrożeniu urządzenia StorSimple i działaniu. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas uruchamiania usługi Microsoft Azure StorSimple. Te informacje dotyczą zarówno urządzenia fizycznego StorSimple w środowisku lokalnym, jak i urządzenia wirtualnego StorSimple.

Na końcu tego artykułu można znaleźć listę kodów błędów, które mogą wystąpić podczas operacji Microsoft Azure StorSimple, a także kroki, które można wykonać, aby rozwiązać błędy. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proces kreatora instalacji urządzeń operacyjnych
Za pomocą kreatora instalacji ([Invoke-HcsSetupWizard][1]) należy sprawdzić konfigurację urządzenia i podjąć działania naprawcze, jeśli to konieczne.

Po uruchomieniu kreatora instalacji na wcześniej skonfigurowanym i operacyjnym urządzeniu przepływ procesu jest inny. Można zmienić tylko następujące wpisy:

* Adres IP, maska podsieci i brama
* Podstawowy serwer DNS
* Podstawowy serwer NTP
* Opcjonalna konfiguracja serwera proxy sieci Web

Kreator instalacji nie wykonuje operacji związanych z zbieraniem haseł i rejestracją urządzenia.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Błędy występujące podczas kolejnych przebiegów kreatora instalacji
W poniższej tabeli opisano błędy, które mogą wystąpić po uruchomieniu kreatora instalacji na urządzeniu operacyjnym, możliwe przyczyny błędów i zalecane akcje, aby je rozwiązać. 

| Nie. | Komunikat lub warunek błędu | Możliwe przyczyny | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1 |Błąd 350032: To urządzenie zostało już dezaktywowane. |Ten błąd zostanie wyświetlony po uruchomieniu kreatora konfiguracji na urządzeniu, które jest dezaktywowane. |Aby uzyskać następujące kroki, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-contact-microsoft-support.md) Nie można umieścić w serwisie dezaktywowanego urządzenia. Przed ponownym uruchomieniem urządzenia może być konieczne przywrócenie ustawień fabrycznych. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Wyjątek od HRESULT: 0x80070001) |Aktualizacja serwera DNS nie powiódł się. Ustawienia DNS są ustawieniami globalnymi i są stosowane we wszystkich włączonych interfejsach sieciowych. |Włącz interfejs i ponownie zastosuj ustawienia DNS. Może to zakłócić działanie sieci dla innych włączonych interfejsów, ponieważ te ustawienia mają charakter globalny. |
| 3 |Urządzenie wydaje się być w trybie online w portalu usługi StorSimple Manager, ale podczas próby ukończenia minimalnej konfiguracji i zapisania konfiguracji operacja kończy się niepowodzeniem. |Podczas wstępnej konfiguracji serwer proxy sieci Web nie został skonfigurowany, mimo że na miejscu był rzeczywisty serwer proxy. |Użyj [polecenia cmdlet Test-HcsmConnection,][2] aby zlokalizować błąd. Jeśli nie możesz rozwiązać problemu, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-contact-microsoft-support.md) |
| 4 |Invoke-HcsSetupWizard: Wartość nie mieści się w oczekiwanym zakresie. |Ten błąd powoduje niepoprawną maskę podsieci. Możliwe przyczyny to: <ul><li> Brak maski podsieci lub jej opróżnienia.</li><li>Format prefiksu Ipv6 jest niepoprawny.</li><li>Interfejs jest włączony w chmurze, ale brakuje bramy lub jest niepoprawna.</li></ul>Należy zauważyć, że data 0 jest automatycznie włączona w chmurze, jeśli jest skonfigurowana za pomocą kreatora konfiguracji. |Aby ustalić problem, należy użyć podsieci 0.0.0.0 lub 256.256.256.256, a następnie spojrzeć na dane wyjściowe. W razie potrzeby wprowadź poprawne wartości maski podsieci, bramy i prefiksu Ipv6. |

## <a name="error-codes"></a>Kody błędów
Błędy są wyświetlane w kolejności numerycznej.

| Numer błędu | Tekst lub opis błędu | Zalecane działanie użytkownika |
|:--- |:--- |:--- |
| 10502 |Podczas uzyskiwania dostępu do konta magazynu wystąpił błąd. |Odczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |
| 40017 |Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ wolumin określony w zasadach tworzenia kopii zapasowej nie został znaleziony na urządzeniu. |Ponów próbę wykonania kopii zapasowej, jeśli błąd będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. kolejnych kroków. |
| 40018 |Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ żaden z woluminów określonych w zasadach tworzenia kopii zapasowych nie został znaleziony na urządzeniu. |Ponów próbę wykonania kopii zapasowej, jeśli błąd będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. kolejnych kroków. |
| 390061 |System jest zajęty lub niedostępny. |Odczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |
| 390143 |Wystąpił błąd z kodem błędu 390143. (Nieznany błąd).) |Jeśli błąd będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |

## <a name="next-steps"></a>Następne kroki
Jeśli nie możesz rozwiązać problemu, [skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-contact-microsoft-support.md) aby uzyskać pomoc. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
