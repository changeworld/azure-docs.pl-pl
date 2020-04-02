---
title: Zalecenia dotyczące zabezpieczeń dla obrazów w portalu Azure Marketplace | Dokumenty firmy Microsoft
description: Ten artykuł zawiera zalecenia dotyczące obrazów uwzględnionych na rynku
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548655"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń dla obrazów portalu Azure Marketplace

Obraz musi spełniać te zalecenia dotyczące konfiguracji zabezpieczeń. Pomaga to utrzymać wysoki poziom zabezpieczeń obrazów rozwiązań partnerskich w portalu Azure Marketplace.

Przed przesłaniem obrazu należy zawsze uruchamiać wykrywanie luk w zabezpieczeniach. W przypadku wykrycia luki w zabezpieczeniach we własnym opublikowanym obrazie należy w odpowiednim czasie poinformować klientów o luce w zabezpieczeniach i o tym, jak ją rozwiązać.

## <a name="open-source-based-images"></a>Obrazy oparte na open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategoria**                                                 | **Sprawdzić**                                                                                                                                                                                                                                                                              |
| Zabezpieczenia                                                     | Zainstaluj wszystkie najnowsze poprawki zabezpieczeń dla dystrybucji Linuksa.                                                                                                                                                                                                              |
| Zabezpieczenia                                                     | Postępuj zgodnie z wytycznymi branżowymi, aby zabezpieczyć obraz maszyny Wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Zabezpieczenia                                                     | Ogranicz obszar ataku, zachowując minimalny ślad przy użyciu tylko niezbędnych ról, funkcji, usług i portów sieciowych systemu Windows Server.                                                                                                                                               |
| Zabezpieczenia                                                     | Skanowanie kodu źródłowego i wynikowego obrazu maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Zabezpieczenia                                                     | Obraz VHD zawiera tylko niezbędne zablokowane konta, które nie mają domyślnych haseł, które umożliwiają interaktywne logowanie; brak tylnych drzwi.                                                                                                                                           |
| Zabezpieczenia                                                     | Wyłącz reguły zapory, chyba że aplikacja funkcjonalnie polega na nich, takich jak urządzenie zapory.                                                                                                                                                                             |
| Zabezpieczenia                                                     | Usuń wszystkie poufne informacje z obrazu VHD, takie jak testowanie kluczy SSH, znanego pliku hosts, plików dziennika i niepotrzebnych certyfikatów.                                                                                                                                       |
| Zabezpieczenia                                                     | Należy unikać stosowania LVM.                                                                                                                                                                                                                                            |
| Zabezpieczenia                                                     | Dołącz najnowsze wersje wymaganych bibliotek: </br> - OpenSSL v1.0 lub większy </br> - Python 2.5 lub wyższy (Python 2.6+ jest wysoce zalecane) </br> - Pakiet Python pyasn1, jeśli nie jest jeszcze zainstalowany </br> - d.OpenSSL v 1.0 lub więcej                                                                |
| Zabezpieczenia                                                     | Wyczyść wpisy historii bash/shell.                                                                                                                                                                                                                                             |
| Networking                                                   | Domyślnie uwzględnij serwer SSH. Ustaw SSH zachować przy życiu do sshd config z następującą opcją: ClientAliveInterval 180.                                                                                                                                                        |
| Networking                                                   | Usuń dowolną niestandardową konfigurację sieci z obrazu. Usuń resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Wdrożenie                                                   | Zainstaluj najnowszego agenta systemu Azure Linux.</br> - Zainstaluj za pomocą pakietu RPM lub Deb.  </br> - Można również użyć procesu instalacji ręcznej, ale pakiety instalatora są zalecane i preferowane. </br> - Jeśli instalujesz agenta ręcznie z repozytorium `waagent` GitHub, najpierw skopiuj plik do `/usr/sbin` i uruchom (jako root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracyjny `/etc/waagent.conf`agenta jest umieszczany w punkcie . |
| Wdrożenie                                                   | Upewnij się, że pomoc techniczna platformy Azure może zapewnić naszym partnerom dane wyjściowe konsoli szeregowej w razie potrzeby i zapewnić odpowiedni limit czasu do montażu dysku systemu operacyjnego z magazynu w chmurze. Dodaj następujące parametry do linii rozruchowej `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`jądra obrazu: . |
| Wdrożenie                                                   | Brak partycji wymiany na dysku systemu operacyjnego. Swap można zażądać do utworzenia na dysku zasobów lokalnych przez agenta systemu Linux.         |
| Wdrożenie                                                   | Utwórz pojedynczą partycję główną dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | Tylko 64-bitowy system operacyjny.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazy z systemem Windows Server

|||
|-------------| -------------------------|
| **Kategoria**                                                     | **Sprawdzić**                                                                                                                                                                |
| Zabezpieczenia                                                         | Użyj bezpiecznego obrazu bazowego systemu operacyjnego. Dysk VHD używany dla źródła dowolnego obrazu opartego na systemie Windows Server musi pochodzić z obrazów systemu operacyjnego Windows Server dostarczonych za pośrednictwem platformy Microsoft Azure. |
| Zabezpieczenia                                                         | Zainstaluj wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Zabezpieczenia                                                         | Aplikacje nie powinny zależeć od ograniczonych nazw użytkowników, takich jak administrator, katalog główny lub administrator.                                                                |
| Zabezpieczenia                                                         | Włącz szyfrowanie dysków funkcją BitLocker zarówno dla dysków twardych systemu operacyjnego, jak i dysków twardych danych.                                                             |
| Zabezpieczenia                                                         | Ogranicz obszar ataku, zachowując minimalny ślad dzięki włączonym tylko niezbędnym rolom, funkcjom, usługom i portom sieciowym systemu Windows Server.                         |
| Zabezpieczenia                                                         | Skanowanie kodu źródłowego i wynikowego obrazu maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Zabezpieczenia                                                         | Ustaw aktualizację zabezpieczeń obrazów systemu Windows Server na automatyczną aktualizację.                                                                                                                |
| Zabezpieczenia                                                         | Obraz VHD zawiera tylko niezbędne zablokowane konta, które nie mają domyślnych haseł, które umożliwiają interaktywne logowanie; brak tylnych drzwi.                             |
| Zabezpieczenia                                                         | Wyłącz reguły zapory, chyba że aplikacja funkcjonalnie polega na nich, takich jak urządzenie zapory.                                                               |
| Zabezpieczenia                                                         | Usuń wszystkie poufne informacje z obrazu VHD, w tym pliki HOSTS, pliki dziennika i niepotrzebne certyfikaty.                                              |
| Wdrożenie                                                       | Tylko 64-bitowy system operacyjny.                            |

Nawet jeśli twoja organizacja nie ma obrazów w portalu Azure Marketplace, należy rozważyć sprawdzenie konfiguracji obrazu systemu Windows i systemu Linux pod kątem tych zaleceń.

## <a name="contacting-customers"></a>Kontakt z klientami

Aby zidentyfikować klientów i ich kontaktowe wiadomości e-mail:

1.  W portalu cloud partner na lewej stronie wybierz pozycję **Insights**.
2.  Na karcie **Zamówienia i użycie** użyj pól Data **rozpoczęcia** i **Data zakończenia,** aby zbadać użycie w wymaganym zakresie dat. Spowoduje to pokazano, które subskrypcje platformy Azure były używane dla oferty na co dzień. Wyeksportuj te dane. 
3.  Podobnie na karcie **Klient zapytaj** i eksportuj bazę klientów.
4.  Dopasuj identyfikator subskrypcji od kroku 2 do identyfikatora subskrypcji z kroku 3, aby znaleźć niezbędne informacje o kliencie.
