---
title: Zarządzanie kontem — rozwiązanie Azure VMware według portalu CloudSimple
description: W tym artykule opisano sposób zarządzania kontami w portalu Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025371"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Zarządzanie kontami w portalu Azure VMware Solution by CloudSimple

Podczas tworzenia usługi CloudSimple, tworzy konto w CloudSimple. Konto jest skojarzone z subskrypcją platformy Azure, w której znajduje się usługa. Wszyscy użytkownicy z rolami właściciela i współautora w subskrypcji mają dostęp do portalu CloudSimple. Identyfikator subskrypcji platformy Azure i identyfikator dzierżawy skojarzony z usługą CloudSimple znajdują się na stronie Konta.

Aby zarządzać kontami w portalu CloudSimple, [przejdź do portalu](access-cloudsimple-portal.md) i wybierz opcję **Konto** w menu bocznym.

Wybierz **Podsumowanie,** aby wyświetlić informacje o konfiguracji CloudSimple twojej firmy. Wyświetlana jest bieżąca pojemność konfiguracji chmury, w tym liczba chmur prywatnych, całkowita pamięć masowa, konfiguracja klastra vSphere, liczba węzłów i liczba rdzeni obliczeniowych. Łącze jest dołączone do zakupu dodatkowych węzłów, jeśli bieżąca konfiguracja nie spełnia wszystkich twoich potrzeb.

## <a name="email-alerts"></a>Alerty e-mail

Możesz dodać adresy e-mail wszystkich osób, które chcesz powiadomić o zmianach w konfiguracji chmury prywatnej.

1. W obszarze **Dodatkowe alerty e-mail** kliknij pozycję **Dodaj nowy**.
2. Wprowadź adres e-mail.
3. Naciśnij przycisk Return.  

Aby usunąć wpis, kliknij przycisk **X**.

## <a name="cloudsimple-operator-access"></a>Dostęp do operatora CloudSimple

Ustawienie dostępu operatora umożliwia CloudSimple, aby pomóc w rozwiązywaniu problemów, umożliwiając inżynierowi pomocy technicznej zalogować się do portalu CloudSimple.  Ustawienie jest domyślnie włączone. Wszystkie działania wykonywane przez inżyniera pomocy technicznej po zalogowaniu się na konto klienta są rejestrowane i dostępne do sprawdzenia na stronie**Inspekcja** **aktywności.** > 

Kliknij przełącznik **włączono dostęp operatora CloudSimple,** aby włączyć lub wyłączyć dostęp.
