---
title: Rozwiązywanie problemów z wdrożonym urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób diagnozowanie i usuwanie błędów występujących na urządzeniu StorSimple, który jest obecnie wdrożone i działa.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: cf037f7f1c1384b654a7144485d38f569eb7c167
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Rozwiązywanie problemów z działającego urządzenia StorSimple
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Przegląd
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów przydatne w celu rozwiązania problemów z konfiguracją czy mogą wystąpić po urządzenia StorSimple jest wdrożone i działa. Opisuje typowych problemów, możliwe przyczyny i zalecane kroki w celu ułatwienia rozwiązywania problemów, które mogą wystąpić po uruchomieniu programu Microsoft Azure StorSimple. Te informacje dotyczą zarówno lokalnego urządzenia fizycznego StorSimple, jak i urządzenia wirtualnego StorSimple.

Na końcu tego artykułu, które można znaleźć listę kodów błędów, które można napotkać podczas operacji Microsoft Azure StorSimple oraz kroki można wykonać, aby naprawić błędy. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proces Kreatora konfiguracji działających urządzeń
Użyj Kreatora konfiguracji ([Invoke HcsSetupWizard][1]) Sprawdź konfigurację urządzenia i podjąć działania naprawcze w razie potrzeby.

Po uruchomieniu Kreatora instalacji na urządzeniu wcześniej skonfigurowana i działa, przepływ procesu różni się. Można zmienić tylko następujące wpisy:

* Adres IP, maski podsieci i bramy
* Podstawowy serwer DNS
* Podstawowy serwer NTP
* Konfiguracja serwera proxy sieci web opcjonalne

Kreator instalacji wykonuje operacje związane z kolekcji i urządzenia rejestracji haseł.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Błędów występujących podczas kolejnych uruchomieniach kreatora konfiguracji
W poniższej tabeli opisano błędy mogą wystąpić podczas uruchamiania Kreatora instalacji na urządzeniu z systemem operacyjny, błędy możliwe przyczyny i zalecane działania, aby je rozwiązać. 

| Nie. | Komunikat o błędzie lub warunku | Możliwe przyczyny | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1 |Błąd 350032: Już zostało zdezaktywowane tego urządzenia. |Zostanie wyświetlony ten błąd, po uruchomieniu Kreatora instalacji na urządzeniu jest dezaktywowana. |[Skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) dalsze czynności. Nie można umieścić dezaktywować urządzenie w usłudze. Resetowanie do ustawień fabrycznych mogą być wymagane w celu aktywowania urządzenia ponownie. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (wyjątek od HRESULT: 0x80070001) |Aktualizacja serwera DNS kończy się niepowodzeniem. Ustawienia DNS są ustawieniami globalnymi i są stosowane przez wszystkie interfejsy sieciowe włączone. |Włącz interfejs i ponownie Zastosuj ustawienia serwera DNS. To może zakłócić sieci dla innych interfejsów włączone, ponieważ te ustawienia są globalne. |
| 3 |Urządzenie wydaje się być w trybie online w portalu usługi Menedżer StorSimple, ale podczas próby wykonania minimalnej konfiguracji i zapisać konfigurację, kończy się niepowodzeniem. |Podczas początkowej konfiguracji serwera proxy sieci web nie skonfigurowano, nawet jeśli było serwera proxy rzeczywistych. |Użyj [polecenia cmdlet Test-HcsmConnection] [ 2] zlokalizować błędu. [Skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) Jeśli nie możesz rozwiązać ten problem. |
| 4 |Invoke-HcsSetupWizard: Wartość jest spoza oczekiwanego zakresu. |Niepoprawna maska podsieci tworzy tego błędu. Możliwe przyczyny to: <ul><li> Maska podsieci jest lub jest pusty.</li><li>Formacie prefiksu Ipv6 jest nieprawidłowy.</li><li>Interfejs jest włączone w chmurze, ale brama jest niewystarczające lub niepoprawne.</li></ul>Należy pamiętać, że interfejs DATA 0 ma automatycznie włączoną obsługę chmury Jeśli skonfigurowana za pośrednictwem Kreatora instalacji. |Aby określić problem, użyj podsieci 0.0.0.0 lub 256.256.256.256 i przyjrzyj się dane wyjściowe. Wprowadź prawidłowe wartości maski podsieci, bramy i prefiks Ipv6, zgodnie z potrzebami. |

## <a name="error-codes"></a>Kody błędów
Błędy są wyświetlane w kolejności numerycznej.

| Numer błędu | Tekst błędu lub opisu | Akcja użytkownika zalecane |
|:--- |:--- |:--- |
| 10502 |Napotkano błąd podczas uzyskiwania dostępu do konta magazynu. |Poczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft dalsze czynności. |
| 40017 |Tworzenie kopii zapasowej nie powiodło się, jak wolumin określonym w zasadach tworzenia kopii zapasowej nie został znaleziony na urządzeniu. |Tworzenie kopii zapasowej ponów operację, jeśli błąd będzie się powtarzać, skontaktuj się z Microsoft Support. dalsze czynności. |
| 40018 |Tworzenie kopii zapasowej nie powiodło się, jak woluminy określonym w zasadach tworzenia kopii zapasowej nie znaleziono żadnych na urządzeniu. |Tworzenie kopii zapasowej ponów operację, jeśli błąd będzie się powtarzać, skontaktuj się z Microsoft Support. dalsze czynności. |
| 390061 |System jest zajęty lub niedostępny. |Poczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft dalsze czynności. |
| 390143 |Wystąpił błąd z kodem błędu 390143. (Nieznany błąd.) |Jeśli błąd będzie się powtarzać, skontaktuj się z Microsoft Support, dalsze czynności. |

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie możesz rozwiązać ten problem, [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) Aby uzyskać pomoc. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
