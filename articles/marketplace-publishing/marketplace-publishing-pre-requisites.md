---
title: Nietechniczna pomoc wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Poznaj wymagania dotyczące tworzenia i wdrażania oferty w portalu Azure Marketplace innym osobom do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: mbaldwin
ms.openlocfilehash: 5c30e62bf345843fe83b3f17b728e1a937d19ce3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29940045"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Ogólne wymagania wstępne dotyczące tworzenia oferty w portalu Azure Marketplace
Informacje ogólne, business procesu skoncentrowane na wstępne, które są potrzebne, aby rozpocząć proces tworzenia oferty.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Upewnij się, że są zarejestrowane jako sprzedawcy z firmą Microsoft
Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania konta sprzedawcy z firmą Microsoft, przejdź do tematu [o tworzeniu konta i rejestracji](marketplace-publishing-accounts-creation-registration.md).

* **Jeśli firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów i chcesz utworzyć nowe oferty,** , a następnie zaloguj się do publikowania portalu o tym samym identyfikatorze poczty e-mail, z którym Centrum deweloperów rejestracja została wykonana. Ten krok jest wymagany, dzięki czemu portalu Centrum deweloperów i publikowaniem są połączone ze sobą.
* **Jeśli firma jest już zarejestrowany jako sprzedawcy w Centrum deweloperów i chcesz edytować istniejące oferty** następnie albo logowanie do publikowania portalu przy użyciu konta administratora lub przy użyciu konta, która jest dodawana jako współadministrator w publikacji portalu. Poniżej podano kroki, aby dodać konto współadministrator.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Kroki, aby dodać współadministrator w portalu publikowania
Administratorzy publikacji portalu można dodać innych członków firmy, którzy pracują w aplikacji, jako współadministrator w publikacji portalu. **Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby dodać co administratora.

> [!NOTE]
> Dla nowych użytkowników przed dodaniem współadministrator w publikacji portalu, upewnij się, że utworzono co najmniej jedną aplikację w publikacji portalu. Jest to wymagane jako **WYDAWCÓW** karcie są wyświetlane tylko w po utworzeniu co najmniej jedną aplikację w publikacji portalu.
> 
> 

1. Upewnij się, że identyfikator wiadomości e-mail współadministrator jest account(MSA) firmy Microsoft. Jeśli nie, zarejestruj go jako MSA, za pomocą tej [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Upewnij się, że istnieje co najmniej jedną aplikację przy użyciu konta administratora, przed podjęciem próby dodania co administratora.
3. Po wykonaniu powyższych czynności są czynności logowania do publikowania portal współadministrator identyfikator wiadomości e-mail, a następnie zaloguj się.
4. Zaloguj się teraz do publikowania portalu o identyfikatorze wiadomości e-mail administratora.
5. Przejdź do wydawcy -> konta -> Wybierz Administratorzy -> Dodaj współadministrator (zrzut ekranu poniżej)
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Upewnij się, że identyfikatory poczty e-mail udostępniane na różnych etapach procesu publikowania (np. Dev Center, publikowania portalu) są monitorowane w przypadku informacji firmy Microsoft.
7. Centrum deweloperów rejestracji należy unikać konta skojarzonego z pojedynczą osobę. Jest to zalecane dla usuwa zależność od jednej osoby.
8. Jeśli czoła problemy z Centrum deweloperów rejestracji, następnie należy spowodować wystąpienie za pomocą tego biletu [łącze](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Kroki, aby usunąć współadministrator w publikacji portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby usunąć co administratora.

1. Zaloguj się do publikowania portalu o identyfikatorze wiadomości e-mail administratora.
2. Przejdź do **wydawców** -> konta -> Wybierz **Administratorzy** -> **Współadministratorzy**.
3. Polecenie **X** przycisk współadministrator ma tot usuwania (zrzut ekranu poniżej).
   
    ![Rysowanie](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Nie masz ukończyć kwota podatku i bank dane firmy, jeśli planowane jest publikowanie oferty tylko wolnego (lub użycie własnej licencji).
> 
> Rejestracji firmy należy wykonać, aby rozpocząć. Jednak gdy firma działa na kwota podatku i bank informacje o koncie Microsoft Developer, deweloperzy mogą rozpocząć pracę na tworzenie obrazu maszyny wirtualnej w [Portal publikowania](https://publish.windowsazure.com), to certyfikowane i testowanie go w środowisku przemieszczania platformy Azure. Konieczne będzie pełną sprzedawcy zatwierdzenia konta tylko do ostatniego kroku publikowania Twojej oferty w portalu Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Uzyskanie subskrypcji platformy Azure "z"
Jest to subskrypcji, która będzie używać do tworzenia obrazów maszyn wirtualnych i przekazać obrazów do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/). Jeśli nie masz istniejącą subskrypcję, następnie zarejestruj się w https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Sprzedaje — od" krajach
> [!WARNING]
> Aby sprzedaży z usług w witrynie Azure Marketplace, upewnij się, że zarejestrowanych jednostka z jednego z krajów zatwierdzonych "sprzedaje — od". To ograniczenie jest ze względu na wypłaty i podatków. Aktywnie czekamy w najbliższej przyszłości Rozwiń tę listę krajów, więc wkrótce. Aby uzyskać pełną listę, zobacz sekcję 1b [zasady uczestnictwa w portalu Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Po nietechnicznych wymagania wstępne są spełnione, obok są oferty określonych technicznych wymagań wstępnych. Kliknij link do artykułu dla typu oferty odpowiednich, który chcesz utworzyć dla portalu Azure Marketplace.

* [Maszyna wirtualna techniczne wymagania wstępne](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Wymagania wstępne techniczne szablon rozwiązania](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

