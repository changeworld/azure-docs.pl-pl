---
title: Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace | Microsoft Docs
description: Ten artykuł zawiera zalecenia dotyczące obrazów uwzględnionych w miejscu na rynku
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b574f7c4f30c3bce8bd7d0e234cb523c965772e1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727585"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace

Firma Microsoft zaleca, aby każde rozwiązanie spełniało następujące zalecenia dotyczące konfiguracji zabezpieczeń. Pozwala to zachować wysoki poziom zabezpieczeń obrazów rozwiązań partnerskich w portalu Azure Marketplace.

Te zalecenia mogą być również przydatne w przypadku organizacji, które nie mają obrazów w portalu Azure Marketplace. Warto zapoznać się z wytycznymi znalezionymi w następujących tabelach w firmowych konfiguracjach obrazów systemów Windows i Linux.

## <a name="open-source-based-images"></a>Obrazy na podstawie otwartych źródeł

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategoria**                                                 | **Niezaznaczone**                                                                                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Zostaną zainstalowane wszystkie najnowsze poprawki zabezpieczeń dotyczące dystrybucji systemu Linux.                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Przestrzegamy wytycznych branżowych zabezpieczających obraz maszyny wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Bezpieczeństwo                                                     | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na niezbędną rolę, funkcje, usługi i porty sieci systemu Windows Server.                                                                                                                                               |
| Bezpieczeństwo                                                     | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Bezpieczeństwo                                                     | Obraz VHD zawiera tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne; Brak tylnych drzwi.                                                                                                                                           |
| Bezpieczeństwo                                                     | Reguły zapory są wyłączone, chyba że aplikacja jest oparta na nich, na przykład na urządzeniu zapory.                                                                                                                                                                             |
| Bezpieczeństwo                                                     | Wszystkie informacje poufne zostały usunięte z obrazu wirtualnego dysku twardego, takich jak testowe klucze SSH, pliki dzienników i niepotrzebne certyfikaty.                                                                                                                                       |
| Bezpieczeństwo                                                     | Zaleca się, aby LVM nie był używany.                                                                                                                                                                                                                                            |
| Bezpieczeństwo                                                     | Należy uwzględnić najnowsze wersje wymaganych bibliotek: </br> -OpenSSL v 1.0 lub nowszy </br> — Python 2,5 lub nowszy (zdecydowanie zalecane jest środowisko Python 2.6 +) </br> — Pakiet pyasn1 języka Python, jeśli nie został jeszcze zainstalowany </br> -d. OpenSSL v 1,0 lub nowszy                                                                |
| Bezpieczeństwo                                                     | Wpisy historii bash/Shell muszą zostać wyczyszczone                                                                                                                                                                                                                                             |
| Networking                                                   | Serwer SSH powinien być domyślnie włączony. Ustaw opcję utrzymywanie aktywności SSH na sshd konfiguracji z następującą opcją: ClientAliveInterval 180                                                                                                                                                        |
| Networking                                                   | Obraz nie powinien zawierać żadnych niestandardowych konfiguracji sieci. Usuń plik resolv. conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Wdrożenie                                                   | Należy zainstalować najnowszego agenta platformy Azure z systemem Linux </br> — Agent należy zainstalować przy użyciu pakietu RPM lub deb.  </br> — Można również użyć procesu instalacji ręcznej, ale pakiety Instalatora są zalecane i preferowane. </br> — Jeśli ręcznie Zainstaluj agenta z repozytorium GitHub, najpierw skopiuj `waagent` plik do `/usr/sbin` i uruchom (jako główny): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracji agenta jest umieszczony pod adresem `/etc/waagent.conf`.    |
| Wdrożenie                                                   | Zapewnia, że dział pomocy technicznej platformy Azure może udostępnić naszym partnerom dane wyjściowe w konsoli szeregowej, jeśli jest to możliwe, i podać odpowiedni limit czasu dla instalacji dysków systemu operacyjnego z magazynu Obraz musi mieć dodane następujące parametry do wiersza rozruchu jądra:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Wdrożenie                                                   | Brak partycji wymiany na dysku systemu operacyjnego. Można zażądać wymiany na lokalnym dysku zasobu przez agenta systemu Linux.         |
| Wdrożenie                                                   | Zaleca się utworzenie pojedynczej partycji głównej dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | 64-bitowy tylko system operacyjny.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazy systemu Windows Server

|||
|-------------| -------------------------|
| **Kategoria**                                                     | **Niezaznaczone**                                                                                                                                                                |
| Bezpieczeństwo                                                         | Użyj bezpiecznego obrazu podstawowego systemu operacyjnego. Wirtualny dysk twardy używany do źródła dowolnego obrazu na podstawie systemu Windows Server musi pochodzić z obrazów systemu operacyjnego Windows Server dostarczonych za pośrednictwem Microsoft Azure. |
| Bezpieczeństwo                                                         | Zainstaluj wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Bezpieczeństwo                                                         | Aplikacje nie powinny być zależne od nazw użytkowników z ograniczeniami, takich jak administrator, root i administrator.                                                                |
| Bezpieczeństwo                                                         | Szyfrowanie dysków funkcją BitLocker nie jest obsługiwana na dysku twardym systemu operacyjnego. Funkcja BitLocker może być używana na dyskach danych.                                                            |
| Bezpieczeństwo                                                         | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na tylko niezbędne role, funkcje, usługi i porty sieci systemu Windows Server.                         |
| Bezpieczeństwo                                                         | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Bezpieczeństwo                                                         | Ustaw aktualizację zabezpieczeń obrazów systemu Windows Server na autoaktualizację.                                                                                                                |
| Bezpieczeństwo                                                         | Obraz VHD zawiera tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne; Brak tylnych drzwi.                             |
| Bezpieczeństwo                                                         | Reguły zapory są wyłączone, chyba że aplikacja jest oparta na nich, na przykład na urządzeniu zapory.                                                               |
| Bezpieczeństwo                                                         | Wszystkie informacje poufne zostały usunięte z obrazu wirtualnego dysku twardego. Na przykład należy usunąć plik HOSTs, pliki dziennika i niepotrzebne certyfikaty.                                              |
| Wdrożenie                                                       | 64-bitowy tylko system operacyjny.                            |
