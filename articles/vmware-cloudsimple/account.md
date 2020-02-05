---
title: Zarządzanie kontami — Portal Azure VMware Solutions (Automatyczna synchronizacja)
description: Opisuje sposób zarządzania kontami w portalu Azure VMware Solutions (Automatyczna synchronizacja)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025371"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Zarządzanie kontami w portalu Azure VMware Solutions (Automatyczna synchronizacja)

Po utworzeniu usługi automatycznej synchronizacji tworzone jest konto o automatycznej synchronizacji. Konto jest skojarzone z subskrypcją platformy Azure, w której znajduje się usługa. Wszyscy użytkownicy z rolami właściciela i współautora w subskrypcji mają dostęp do portalu automatycznej synchronizacji. Identyfikator subskrypcji platformy Azure i identyfikator dzierżawy skojarzone z usługą automatycznej synchronizacji są dostępne na stronie konta.

Aby zarządzać kontami w portalu automatycznej synchronizacji, [uzyskaj dostęp do portalu](access-cloudsimple-portal.md) i wybierz pozycję **konto** w menu po stronie.

Wybierz pozycję **Podsumowanie** , aby wyświetlić informacje o konfiguracji automatycznej synchronizacji Twojej firmy. Zostanie wyświetlona aktualna pojemność konfiguracji chmury, w tym liczba chmur prywatnych, całkowity magazyn, Konfiguracja klastra vSphere, liczba węzłów i liczba rdzeni obliczeniowych. Łącze jest dołączone do zakupu dodatkowych węzłów, jeśli Bieżąca konfiguracja nie spełnia wszystkich Twoich potrzeb.

## <a name="email-alerts"></a>Alerty e-mail

Możesz dodać adresy e-mail osób, które mają być powiadamiane o zmianach w konfiguracji chmury prywatnej automatycznej synchronizacji.

1. W obszarze **dodatkowe alerty e-mail** kliknij pozycję **Dodaj nowy**.
2. Wprowadź adres e-mail.
3. Naciśnij klawisz Return.  

Aby usunąć wpis, kliknij przycisk **X**.

## <a name="avs-operator-access"></a>Dostęp do operatora automatycznej synchronizacji

Ustawienie dostęp operatora umożliwia automatyczna synchronizacja, która pomaga w rozwiązywaniu problemów, umożliwiając inżynierowi pomocy technicznej zalogowanie się do portalu automatycznej rejestracji. Ustawienie jest domyślnie włączone. Wszystkie akcje wykonywane przez inżyniera pomocy technicznej po zalogowaniu się na koncie klienta są rejestrowane i dostępne do przeglądu na stronie **inspekcji** > **działań** .

Kliknij przełącznik **Automatyczna synchronizacja z włączonym dostępem operatora** , aby włączyć lub wyłączyć dostęp.
