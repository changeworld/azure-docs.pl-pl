---
title: Rozwiązywanie problemów z wdrożonym urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Opisano, jak diagnozować i rozwiązywać błędy występujące na urządzeniu StorSimple, która jest obecnie wdrożona i operacyjnej.
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
ms.openlocfilehash: 8ad3f09bf46caf426b2008b583ebd2ff78522462
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302521"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Rozwiązywanie problemów operacyjnych urządzenia StorSimple
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [— często zadawane pytania: Przenieś do witryny Azure portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Omówienie
Ten artykuł zawiera przydatne wskazówki dotyczące rozwiązywania problemów w celu rozwiązywania problemów z konfiguracją, mogą wystąpić po urządzenia StorSimple jest wdrożone i działa. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki, aby pomóc w rozwiązywaniu problemów, które mogą wystąpić, gdy uruchamiane jest Microsoft Azure StorSimple. Te informacje dotyczą zarówno w przypadku urządzenia fizycznego StorSimple w środowisku lokalnym, jak i urządzenia wirtualnego StorSimple.

Na końcu tego artykułu, który można znaleźć listę kodów błędów, które można napotkać podczas operacji programu Microsoft Azure StorSimple a także kroki można wykonać, aby naprawić błędy. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proces Kreatora instalacji operacyjnej urządzeń
Użyj Kreatora konfiguracji ([polecenie Invoke-HcsSetupWizard][1]) Sprawdź konfigurację urządzenia i podejmij działania naprawcze, jeśli to konieczne.

Po uruchomieniu Kreatora konfiguracji na urządzeniu wcześniej skonfigurowane i operacyjnych, przepływ procesu jest inny. Można zmienić tylko następujące wpisy:

* Adres IP, maskę podsieci i bramę
* Podstawowy serwer DNS
* Podstawowy serwer NTP
* Konfiguracja serwera proxy sieci web opcjonalne

Kreator instalacji nie wykonuje operacje związane z kolekcji i urządzenia rejestracji haseł.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Błędy występujące podczas kolejnych przebiegów Kreatora konfiguracji
W poniższej tabeli opisano błędy mogą wystąpić po uruchomieniu Kreatora instalacji na urządzeniu z systemem operacyjnej, możliwe przyczyny błędów i rekomendowanych działań umożliwiających ich rozwiązywania. 

| Nie. | Komunikat o błędzie lub warunku | Możliwe przyczyny | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1 |Błąd 350032: To urządzenie zostało już zdezaktywowane. |Po uruchomieniu Kreatora konfiguracji na urządzeniu, które jest dezaktywowany, zostanie wyświetlony ten błąd. |[Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-contact-microsoft-support.md), aby uzyskać informacje o kolejnych krokach. Dezaktywowanego urządzenia nie można umieścić w usłudze. Reset do ustawień fabrycznych może być wymagane w celu aktywowania urządzenie ponownie. |
| 2 |Polecenie Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (wyjątek od HRESULT: 0x80070001) |Aktualizacja serwera DNS kończy się niepowodzeniem. Ustawienia DNS są ustawieniami globalnymi i są stosowane do wszystkich interfejsów sieciowych włączone. |Włącz interfejs, a następnie ponownie Zastosuj ustawienia DNS. Może to spowodować zakłócenia sieci dla innych interfejsów włączone, ponieważ te ustawienia są globalne. |
| 3 |Urządzenie wydaje się być w trybie online w portalu usługi StorSimple Manager, ale gdy spróbujesz przeprowadzić minimalną konfigurację i zapisać konfigurację, kończy się niepowodzeniem. |Podczas początkowej konfiguracji serwera proxy sieci web nie została skonfigurowana, nawet jeśli została serwer proxy rzeczywiste. |Użyj [polecenia cmdlet Test-HcsmConnection] [ 2] zlokalizować błędu. [Skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) Jeśli nie możesz rozwiązać ten problem. |
| 4 |Polecenie Invoke-HcsSetupWizard: Wartość nie należy do oczekiwanego zakresu. |Niepoprawna maska podsieci generuje ten błąd. Możliwe przyczyny to: <ul><li> Maska podsieci jest brakującym lub pustym.</li><li>W formacie prefiksu Ipv6 jest nieprawidłowy.</li><li>Interfejs jest włączone w chmurze, ale brama jest brakujące lub nieprawidłowe.</li></ul>Należy pamiętać, że interfejs DATA 0 ma automatycznie włączoną obsługę chmury Jeśli skonfigurowane za pomocą Kreatora instalacji. |Aby ustalić problem, użyj podsieci 0.0.0.0 lub 256.256.256.256, a następnie Przyjrzyj się dane wyjściowe. Wprowadź poprawne wartości maski podsieci, bramy i prefiks Ipv6, zgodnie z potrzebami. |

## <a name="error-codes"></a>Kody błędów
Błędy są wyświetlane w kolejności numerycznej.

| Numer błędu | Tekst błędu lub opisu | Akcja zalecana użytkownika |
|:--- |:--- |:--- |
| 10502 |Napotkano błąd podczas uzyskiwania dostępu do konta magazynu. |Poczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z skontaktuj się z pomocą techniczną firmy Microsoft dla następnych kroków. |
| 40017 |Wykonywanie kopii zapasowej nie powiodło się, ponieważ nie można odnaleźć woluminu, określonym w zasadach tworzenia kopii zapasowej na urządzeniu. |Ponów próbę wykonania kopii zapasowej operacji, jeśli błąd będzie się powtarzać, skontaktuj się z Microsoft Support. dalsze czynności. |
| 40018 |Wykonywanie kopii zapasowej nie powiodło się, ponieważ znaleziono żadnych z określonych w zasadach kopii zapasowych woluminów na urządzeniu. |Ponów próbę wykonania kopii zapasowej operacji, jeśli błąd będzie się powtarzać, skontaktuj się z Microsoft Support. dalsze czynności. |
| 390061 |System jest zajęty lub niedostępny. |Poczekaj kilka minut, a następnie spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z skontaktuj się z pomocą techniczną firmy Microsoft dla następnych kroków. |
| 390143 |Wystąpił błąd z kodem błędu 390143. (Nieznany błąd.) |Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą firmy Microsoft Support dla następnych kroków. |

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie możesz rozwiązać ten problem, [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md) uzyskać pomoc. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
