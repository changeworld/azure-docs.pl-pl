---
title: Nietechniczna wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Poznaj wymagania dotyczące tworzenia i wdrażania oferty w portalu Azure Marketplace przez inne osoby do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
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
ROBOTS: NOINDEX
ms.openlocfilehash: 4b925522186d2d9ae537431c1d96d39b107ad967
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073173"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Ogólne wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace
Informacje o wymaganiach wstępnych ogólne, biznesowymi do procesu, które są potrzebne, aby rozpocząć proces tworzenia oferty.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Upewnij się, że zarejestrowano Cię jako sprzedawcy firmy Microsoft
Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania konta sprzedawcy firmy Microsoft, przejdź do [o tworzeniu konta i rejestracji](marketplace-publishing-accounts-creation-registration.md).

* Jeśli Twoja firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów i chcesz utworzyć nową ofertę, następnie zaloguj się do publikowania w portalu przy użyciu tego samego Identyfikatora poczty e-mail za pomocą Centrum deweloperów, które odbywa się rejestracji. Ten krok jest wymagany, dzięki czemu Centrum deweloperów i publikowania w portalu są połączone ze sobą.
* Jeśli Twoja firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów i chcesz edytować istniejące oferty, a następnie zaloguj się do publikowania w portalu przy użyciu konta administratora lub konta, który jest dodawany jako coadmin w publikowania portalu. Kroki, aby dodać konto coadmin są podane poniżej.

## <a name="steps-to-add-a-coadmin-in-the-publishing-portal"></a>Kroki, aby dodać coadmin w portalu wydawców
Administratorzy publikacji portalu można dodać innych członków firmy, którzy pracują nad aplikacją, jako coadmin podczas publikowania w portalu. **Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby dodać coadmin.

> [!NOTE]
> Dla nowych użytkowników przed dodaniem współadministrator podczas publikowania w portalu, upewnij się, że utworzono co najmniej jedną aplikację w publikowania portalu. Jest to wymagane jako **WYDAWCÓW** karty są wyświetlane tylko po to, po utworzeniu co najmniej jedną aplikację w publikowania portalu.
> 
> 

1. Upewnij się, że identyfikator poczty e-mail coadmin account(MSA) firmy Microsoft. Jeśli nie, zarejestruj go jako za pomocą tego konta Microsoft [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Upewnij się, że istnieje co najmniej jedną aplikację przy użyciu konta administratora, zanim spróbujesz dodać coadmin.
3. Po ukończeniu powyższych kroków, zaloguj się do do publikowania witryny portal przy użyciu coadmin identyfikator e-mail, a następnie zaloguj.
4. Teraz logować się do publikowania w portalu przy użyciu identyfikatora administratora poczty e-mail.
5. Przejdź do wydawcy -> Wybierz -> konta administratorów -> Dodaj coadmin (zrzut ekranu, podane poniżej)
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Upewnij się, że identyfikatory poczty e-mail udostępniane na różnych etapach procesu publikowania (na przykład Centrum deweloperów, portal wydawców) są monitorowane pod kątem jakiejkolwiek korespondencji od firmy Microsoft.
7. Centrum deweloperów rejestracji należy unikać konta skojarzonego z jedną osobę. Tę sugestię usuwa zależność od jedna osoba.
8. Jeśli napotkasz problemy z rejestracją Centrum deweloperów, zgłosić bilet za pomocą tego [łącze](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-coadmin-in-the-publishing-portal"></a>Kroki, aby usunąć coadmin podczas publikowania w portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby usunąć coadmin.

1. Zaloguj się do publikowania portalu identyfikatorem adres e-mail administratora
2. Przejdź do **wydawców** -> Wybierz -> Twoje konto **Administratorzy** -> **Współadministratorzy**.
3. Kliknij pozycję **X** przycisk znajdujący się obok coadmin ma tot usuwania (zrzut ekranu, podane poniżej).
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Nie trzeba wykonać informacje podatkowe i bankowe firmy, jeśli zamierzasz opublikować tylko bezpłatnych ofert (lub model dostarczania własnej licencji).
> 
> Aby rozpocząć pracę, należy wykonać rejestracji firmy. Jednak gdy firma pracuje na informacje podatkowe i bankowe, w ramach konta Microsoft Developer, deweloperzy mogą rozpocząć pracę na temat tworzenia obrazu maszyny wirtualnej w [portalu wydawców](https://publish.windowsazure.com), pobierania ich certyfikowane i testowania go w środowisko tymczasowe systemu Azure. Zatwierdzenie konta sprzedawcy pełną należy tylko w ostatnim kroku opublikowania oferty w portalu Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Uzyskiwanie subskrypcji platformy Azure "zgodnie z rzeczywistym użyciem"
Ta wartość jest subskrypcję, która będzie używać do tworzenia obrazów maszyn wirtualnych i przekazać obrazy [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/). Jeśli nie masz istniejącą subskrypcję, następnie zarejestruj się w https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Sprzedaż — od" kraje
> [!WARNING]
> Aby sprzedawać swoje usługi w portalu Azure Marketplace, upewnij się, że zarejestrowane jednostki z jednego z krajów zatwierdzonych "sprzedaż — od". To ograniczenie jest ze względu na informacje dotyczące wypłat i opodatkowania. Firma Microsoft aktywnie chcą rozwiń ta lista krajów w najbliższej przyszłości, więc wkrótce. Aby uzyskać pełną listę, zobacz 1b sekcji [zasad uczestnictwa w portalu Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Po Nietechniczne wymagania wstępne są spełnione, są następnie techniczne wymagania wstępne specyficzne dla oferty. Kliknij link do artykułu dla typu odpowiednich oferty, który chcesz utworzyć w portalu Azure Marketplace.

* [Techniczne wymagania wstępne maszyny Wirtualnej](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Rozwiązanie szablonu techniczne wymagania wstępne](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

