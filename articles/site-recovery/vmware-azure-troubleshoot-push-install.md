---
title: Usługa Azure Site Recovery rozwiązywania problemów z programu VMware do platformy Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędami podczas replikowania maszyn wirtualnych platformy Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 07/06/2018
ms.openlocfilehash: 8d5db03eeebb659414ea1f554e5b34c938fd2795
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952913"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności

W tym artykule opisano, jak rozwiązywać typowe błędy, które może być twarzy przy próbie zainstalowania usługi mobilności Azure Site Recovery na serwerze źródłowym, aby włączyć ochronę.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Błąd 78007 — nie można ukończyć żądanej operacji
Ten błąd może zostać wygenerowany przez usługę z kilku powodów. Wybierz odpowiedni błąd dostawcy, rozwiązywać problemy z dalszych.

* [Błąd 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Błąd 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Błąd 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Błąd 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Błąd 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Błąd 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Błąd 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Błąd 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Błąd 95105 - ochrony nie można włączyć (EP0856)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95105 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej nie powiodła się z kodem błędu **EP0856**. <br> Albo **udostępnianie plików i drukarek** nie jest dozwolona w źródle maszynowo lub występują są sieci problemy z łącznością między serwerem przetwarzania a maszyną źródłową.| **Udostępnianie plików i drukarek** nie jest włączona. | Zezwalaj na **udostępnianie plików i drukarek** na maszynie źródłowej w Zaporze Windows. Na maszynie źródłowej w obszarze **zapory Windows** > **Zezwalaj aplikacji lub funkcji za pośrednictwem zapory**, wybierz opcję **udostępnianie plików i drukarek we wszystkich profilach**. </br> Ponadto sprawdź następujące wymagania wstępne do pomyślnego zakończenia instalacji wypychanej.<br> Przeczytaj więcej na temat [Rozwiązywanie problemów z usługą WMI wystawia](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Błąd 95107 - ochrony nie można włączyć (EP0858)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95107 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej nie powiodła się z kodem błędu **EP0858**. <br> Poświadczenia podane zainstalować usługi mobilności są niepoprawne lub konto użytkownika ma niewystarczające uprawnienia. | Poświadczenia użytkownika do zainstalowania usługi mobilności na maszynie źródłowej są nieprawidłowe. | Upewnij się, że poświadczenia użytkownika dostarczone dla maszyny źródłowej na serwerze konfiguracji są poprawne. <br> Aby dodać lub edytować poświadczeń użytkownika, przejdź do serwera konfiguracji i wybierz **Cspsconfigtool** > **Zarządzanie kontem**. </br> Ponadto, sprawdź następujące [wymagania wstępne](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) do pomyślnego zakończenia instalacji wypychanej.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Błąd 95117 - ochrony nie można włączyć (EP0865)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95117 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej nie powiodła się z kodem błędu **EP0865**. <br> Maszyna źródłowa nie jest uruchomiona lub występują problemy z łącznością sieciową między serwerem przetwarzania a maszyną źródłową. | Problemy z łącznością sieciową między serwerem przetwarzania i serwera źródłowego. | Sprawdź łączność między serwerem przetwarzania a na serwerze źródłowym. </br> Ponadto, sprawdź następujące [wymagania wstępne](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) do pomyślnego zakończenia instalacji wypychanej.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Błąd 95103 - ochrony nie można włączyć (EP0854)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95103 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej nie powiodła się z kodem błędu **EP0854**. <br> Instrumentacja zarządzania Windows (WMI) nie jest dozwolona na maszynie źródłowej lub występują problemy z łącznością sieciową między serwerem przetwarzania a maszyną źródłową.| Usługa WMI jest zablokowany w Zaporze Windows. | Usługa WMI w Zaporze Windows. W obszarze **zapory Windows** > **Zezwalaj aplikacji lub funkcji za pośrednictwem zapory**, wybierz opcję **WMI dla wszystkich profilów**. </br> Ponadto, sprawdź następujące [wymagania wstępne](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) do pomyślnego zakończenia instalacji wypychanej.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Błąd 95213 - ochrony nie można włączyć (EP0874)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95213 </br>**Komunikat o błędzie:** instalacji usługi Mobility na źródłowej maszynie % SourceIP; nie powiodło się z kodem błędu **EP0874**. <br> | Wersja systemu operacyjnego na maszynie źródłowej nie jest obsługiwane. <br>| Upewnij się, że maszyna źródłowa jest obsługiwana wersja systemu operacyjnego. Odczyt [macierz obsługi](https://aka.ms/asr-os-support). </br> Ponadto, sprawdź następujące [wymagania wstępne](https://aka.ms/pushinstallerror) do pomyślnego zakończenia instalacji wypychanej.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Błąd 95108 - ochrony nie można włączyć (EP0859)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95108 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej nie powiodła się z kodem błędu **EP0859**. <br>| Poświadczenia podane zainstalować usługi mobilności są niepoprawne lub konto użytkownika ma niewystarczające uprawnienia. <br>| Upewnij się, że podane poświadczenia są **głównego** poświadczeń konta. Aby dodać lub edytować poświadczeń użytkownika, przejdź do serwera konfiguracji, a następnie wybierz **Cspsconfigtool** ikonę skrótu na pulpicie. Wybierz **Zarządzanie kontem** umożliwiają dodawanie lub edytowanie poświadczeń.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Błąd 95265 - ochrony nie można włączyć (EP0902)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95265 </br>**Komunikat o błędzie:** instalacji wypychanej usługi mobilności na maszynie źródłowej powiodła się, ale maszyna źródłowa wymaga ponownego uruchomienia dla niektórych zmiany w systemie zaczęły obowiązywać. <br>| Starszą wersję usługi mobilności został już zainstalowany na serwerze.| Replikacja maszyny wirtualnej nadal bezproblemowo.<br> Uruchom ponownie serwer podczas następnego okna obsługi w taki sposób, aby uzyskać korzyści wynikające z nowych ulepszeń usługi mobilności.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Błąd 95224 - ochrony nie można włączyć (EP0883)

**Kod błędu:** | **Możliwe przyczyny** | **Zalecenia dotyczące błędów**
--- | --- | ---
95224 </br>**Komunikat o błędzie:** wypychana instalacji usługi mobilności na maszynie źródłowej % SourceIP; nie powiodła się. kod błędu **EP0883**. Oczekuje na ponowne uruchomienie systemu z poprzedniej instalacji lub aktualizacji.| Nie można ponownie uruchomić system, podczas odinstalowywania starszej lub niezgodna wersja usługi mobilności.| Upewnij się, że nie ma wersji usługi mobilności istnieje na serwerze. <br> Uruchom ponownie serwer, a następnie uruchom ponownie zadanie włączania ochrony.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Zasób, aby rozwiązać problemy z instalacją wypychaną

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Rozwiązywanie problemów z plików i wydruku udostępniania problemów
* [Włącz lub wyłącz udostępnianie za pomocą zasad grupy plików](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Włącz udostępnianie plików i drukarek przez zaporę Windows](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Rozwiązywanie problemów z usługą WMI
* [Podstawowe testy usługi WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z usługą WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Rozwiązywanie problemów ze skryptami WMI i usługi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.