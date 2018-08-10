---
title: Nietechniczna wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Poznaj wymagania dotyczące tworzenia i wdrażania oferty w portalu Azure Marketplace przez inne osoby do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ms.openlocfilehash: 4f86d444a2f2b97fd8605d480db358813bc39fd3
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714193"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Ogólne wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace
Informacje o wymaganiach wstępnych ogólne, biznesowymi do procesu, które są potrzebne, aby rozpocząć proces tworzenia oferty.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Upewnij się, że zarejestrowano Cię jako sprzedawcy firmy Microsoft
Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania konta sprzedawcy firmy Microsoft, przejdź do [o tworzeniu konta i rejestracji](marketplace-publishing-accounts-creation-registration.md).

* **Jeśli Twoja firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów i chcesz utworzyć nową ofertę** , a następnie zaloguj się do publikowania w portalu przy użyciu tego samego identyfikatora poczty e-mail za pomocą Centrum deweloperów, które odbywa się rejestracji. Ten krok jest wymagany, dzięki czemu Centrum deweloperów i publikowania w portalu są połączone ze sobą.
* **Jeśli Twoja firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów, a chcesz edytować istniejące oferty** następnie albo Zaloguj się do publikowania portalu przy użyciu konta administratora lub konta, która jest dodawana jako współadministratora w publikowania portalu. Kroki, aby dodać konta współadministratora są podane poniżej.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Kroki, aby dodać współadministrator w portalu wydawców
Administratorzy publikacji portalu można dodać inni członkowie firmie, którzy pracują nad aplikacją, jako współadministratora w publikowania portalu. **Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby dodać współadministratora.

> [!NOTE]
> Dla nowych użytkowników przed dodaniem współadministrator podczas publikowania w portalu, upewnij się, że utworzono co najmniej jedną aplikację w publikowania portalu. Jest to wymagane jako **WYDAWCÓW** karty są wyświetlane tylko po to, po utworzeniu co najmniej jedną aplikację w publikowania portalu.
> 
> 

1. Upewnij się, że identyfikator poczty e-mail współadministrator account(MSA) firmy Microsoft. Jeśli nie, zarejestruj go jako zarządzanych kont usług, za pomocą tego [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Upewnij się, że istnieje co najmniej jedną aplikację przy użyciu konta administratora, zanim spróbujesz dodać współadministratora.
3. Po wykonaniu czynności powyższe kroki, zaloguj się do publikowania portalu z współadministrator identyfikator poczty e-mail, a następnie, w przypadku wylogowania.
4. Teraz Zaloguj się do publikowania w portalu przy użyciu identyfikatora poczty e-mail administratora.
5. Przejdź do wydawcy -> Wybierz -> konta administratorów -> Dodaj współadministrator (zrzut ekranu, podane poniżej)
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Upewnij się, że identyfikatory poczty e-mail udostępniane na różnych etapach procesu publikowania (np. Dev Center, portal wydawców) są monitorowane pod kątem jakiejkolwiek korespondencji od firmy Microsoft.
7. Centrum deweloperów rejestracji należy unikać konta skojarzonego z jedną osobę. Jest to zalecane usunięcie zależności od jednej osoby.
8. Jeśli napotkasz problemy z rejestracją Centrum deweloperów, następnie Zgłoś bilet za pomocą tego [łącze](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Kroki, aby usunąć współadministrator podczas publikowania w portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby usunąć współadministratora.

1. Zaloguj się do publikowania w portalu przy użyciu identyfikatora poczty e-mail administratora.
2. Przejdź do **wydawców** -> Wybierz -> Twoje konto **Administratorzy** -> **Współadministratorzy**.
3. Kliknij pozycję **X** przycisk znajdujący się obok współadministrator ma tot usuwania (zrzut ekranu, podane poniżej).
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Nie trzeba wykonać informacje podatkowe i bankowe firmy, jeśli zamierzasz opublikować tylko bezpłatnych ofert (lub model dostarczania własnej licencji).
> 
> Aby rozpocząć pracę, należy wykonać rejestracji firmy. Jednak gdy firma pracuje na informacje podatkowe i bankowe, w ramach konta Microsoft Developer, deweloperzy mogą rozpocząć pracę na temat tworzenia obrazu maszyny wirtualnej w [portalu wydawców](https://publish.windowsazure.com), pobierania ich certyfikowane i testowania go w środowisko tymczasowe systemu Azure. Zatwierdzenie konta sprzedawcy pełną należy tylko w ostatnim kroku opublikowania oferty w portalu Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Uzyskiwanie subskrypcji platformy Azure "zgodnie z rzeczywistym użyciem"
Jest to subskrypcję, która będzie używać do tworzenia obrazów maszyn wirtualnych i przekazać obrazy na [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/). Jeśli nie masz istniejącą subskrypcję, następnie zarejestruj się w https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Sprzedaż — od" kraje
> [!WARNING]
> Aby sprzedawać swoje usługi w portalu Azure Marketplace, upewnij się, że zarejestrowane jednostki z jednego z krajów zatwierdzonych "sprzedaż — od". To ograniczenie jest ze względu na informacje dotyczące wypłat i opodatkowania. Firma Microsoft aktywnie chcą rozwiń ta lista krajów w najbliższej przyszłości, więc wkrótce. Aby uzyskać pełną listę, zobacz 1b sekcji [zasad uczestnictwa w portalu Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Po Nietechniczne wymagania wstępne są spełnione, obok są oferty określonych techniczne wymagania wstępne. Kliknij link do artykułu dla typu odpowiednich oferty, który chcesz utworzyć w portalu Azure Marketplace.

* [Techniczne wymagania wstępne maszyny Wirtualnej](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Rozwiązanie szablonu techniczne wymagania wstępne](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

