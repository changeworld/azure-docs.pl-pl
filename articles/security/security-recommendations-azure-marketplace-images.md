---
title: Zalecenia dotyczące zabezpieczeń dla obrazów Azure Marketplace | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zalecenia dotyczące obrazów zawartych na rynek
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931135"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń dla obrazów Azure Marketplace

Zaleca się, że każde rozwiązanie spełniają poniższe zalecenia konfiguracji zabezpieczeń. Ułatwi to utrzymanie wysokiego poziomu zabezpieczeń dla partnera rozwiązania obrazów w portalu Azure Marketplace.

Zalecenia te mogą być również przydatne w przypadku organizacji, które nie mają obrazów w witrynie Azure marketplace. Możesz sprawdzić konfiguracje firmy systemu Windows i Linux obrazu względem wytyczne znaleźć w poniższych tabelach.

## <a name="open-source-based-images"></a>Otwórz obrazy na podstawie źródła

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategoria**                                                 | **Sprawdź**                                                                                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Są zainstalowane wszystkie najnowsze poprawki zabezpieczenia dla dystrybucji systemu Linux.                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Wykonano branżowych wytycznych dotyczących do zabezpieczania obrazu maszyny Wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Bezpieczeństwo                                                     | Ogranicz obszar narażony na ataki przy zachowaniu minimalnego rozmiaru z tylko niezbędnych ról systemu Windows Server, funkcji, usług i portów sieciowych.                                                                                                                                               |
| Bezpieczeństwo                                                     | Skanowanie kodu źródłowego i wynikowy obrazu maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Bezpieczeństwo                                                     | Obraz dysku VHD zawiera tylko niezbędne zablokowane konta, które nie mają hasła domyślne, które umożliwiałyby logowania interakcyjnego; nie drzwi Wstecz.                                                                                                                                           |
| Bezpieczeństwo                                                     | Reguły zapory są wyłączone, chyba że aplikacji funkcjonalnie opiera się na nich, takiego jak urządzenie zapory.                                                                                                                                                                             |
| Bezpieczeństwo                                                     | Wszystkie informacje poufne została usunięta z obrazu wirtualnego dysku twardego, takie jak klucze SSH testu, znane pliku hosts, plików dziennika i niepotrzebne certyfikatów.                                                                                                                                       |
| Bezpieczeństwo                                                     | Zaleca się, że LVM nie powinny być używane.                                                                                                                                                                                                                                            |
| Bezpieczeństwo                                                     | Najnowsze wersje wymaganych bibliotek należy dołączyć: </br> -Biblioteki OpenSSL w wersji 1.0 lub nowszej </br> -Python 2.5 lub nowszej (Python 2.6 + jest zdecydowanie zalecane) </br> -Python pyasn1 pakietu, jeśli nie jest jeszcze zainstalowany </br> -v d.OpenSSL wersji 1.0 lub nowszej                                                                |
| Bezpieczeństwo                                                     | Wpisów historii powłoki bash/muszą zostać wyczyszczone.                                                                                                                                                                                                                                             |
| Sieć                                                   | Domyślnie powinien znajdować się serwer SSH. Ustawioną SSH keep alive config sshd z następujących opcji: ClientAliveInterval 180                                                                                                                                                        |
| Sieć                                                   | Obraz nie może zawierać żadnej konfiguracji sieci niestandardowej. Usuń resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Wdrożenie                                                   | Należy zainstalować najnowszą wersję agenta systemu Linux platformy Azure </br> — Agenta należy zainstalować przy użyciu pakietu obr. / min lub Deb.  </br> — Można także użyć proces ręcznej instalacji, ale pakietów Instalatora są zalecane i preferowany. </br> — W przypadku ręcznego instalowania agenta z repozytorium github, najpierw skopiować `waagent` pliku `/usr/sbin` i uruchom (jako główny): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracji agenta zostaną umieszczone w `/etc/waagent.conf`.    |
| Wdrożenie                                                   | Zapewnia, że obsługa Azure zapewnia naszych partnerów z konsoli szeregowej dane wyjściowe w przypadku potrzebne i podaj odpowiednie limitu czasu dla instalowanie dysku systemu operacyjnego z magazynu w chmurze. Obraz musi dodać następujące parametry wiersza rozruchu jądra:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Wdrożenie                                                   | Nie wymiany partycji na dysku systemu operacyjnego. Wymiany może być wysłane do utworzenia na dysku lokalnym zasobów przez agenta systemu Linux.         |
| Wdrożenie                                                   | Zaleca się, że partycji z jednym elementem głównym jest tworzony dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | 64-bitowych tylko system operacyjny.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazów na serwerze systemu Windows

|||
|-------------| -------------------------|
| **Kategoria**                                                     | **Sprawdź**                                                                                                                                                                |
| Bezpieczeństwo                                                         | Użyj bezpiecznego podstawowy obraz systemu operacyjnego. Wirtualny dysk twardy używany dla źródła żadnego obrazu oparte na systemie Windows Server musi być z obrazów systemu operacyjnego Windows Server przez Microsoft Azure. |
| Bezpieczeństwo                                                         | Należy zainstalować wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Bezpieczeństwo                                                         | Aplikacji nie powinna mieć zależność o nazwach ograniczeniami użytkownika, takie jak Administrator, głównego i administratora.                                                                |
| Bezpieczeństwo                                                         | Szyfrowanie dysków funkcją BitLocker nie jest obsługiwana na dysku twardym systemu operacyjnego. Funkcja BitLocker może być używana dla dysków z danymi.                                                            |
| Bezpieczeństwo                                                         | Ogranicz obszar narażony na ataki przy zachowaniu minimalnego rozmiaru z tylko niezbędne systemu Windows Server role, funkcje, usług i portów sieciowych włączone.                         |
| Bezpieczeństwo                                                         | Skanowanie kodu źródłowego i wynikowy obrazu maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Bezpieczeństwo                                                         | Ustaw aktualizacji zabezpieczeń obrazów systemu Windows Server w celu automatycznej aktualizacji.                                                                                                                |
| Bezpieczeństwo                                                         | Obraz dysku VHD zawiera tylko niezbędne zablokowane konta, które nie mają hasła domyślne, które umożliwiałyby logowania interakcyjnego; nie drzwi Wstecz.                             |
| Bezpieczeństwo                                                         | Reguły zapory są wyłączone, chyba że aplikacji funkcjonalnie opiera się na nich, takiego jak urządzenie zapory.                                                               |
| Bezpieczeństwo                                                         | Wszystkie informacje poufne została usunięta z obrazu wirtualnego dysku twardego. Na przykład można usunąć pliku hostów, plików dziennika i niepotrzebne certyfikatów.                                              |
| Wdrożenie                                                       | 64-bitowych tylko system operacyjny.                            |
