---
title: Tworzenie dzierżawy usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat dotyczący sposobu tworzenia dzierżawy usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d1031074885b0ca55d0b1647575354fcd0f49889
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712270"
---
# <a name="create-an-azure-active-directory-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure Active Directory B2C

Ta opcja szybkiego startu ułatwia tworzenie dzierżawy usługi Microsoft Azure Active Directory (Azure AD) B2C w ciągu kilku minut. Po zakończeniu, masz dzierżawę B2C (katalog) na potrzeby rejestrowania aplikacji B2C.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Nie można włączyć funkcji B2C w istniejących dzierżawców. Musisz utworzyć dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Gratulacje, utworzono dzierżawy usługi Azure Active Directory B2C. Jesteś administratorem globalnym dzierżawcy. W razie potrzeby możesz dodawać innych administratorów globalnych. Aby przełączyć się do nowej dzierżawy, kliknij przycisk *Zarządzanie nowej dzierżawy*.

![Zarządzanie nowe łącze dzierżawy](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Jeśli planujesz używać dzierżawy B2C do aplikacji produkcyjnej, przeczytaj artykuł dotyczący [skali produkcji a dzierżawcy usługi B2C w wersji zapoznawczej](active-directory-b2c-reference-tenant-type.md). Istnieją znane problemy podczas usuwania istniejącej dzierżawy B2C i utwórz go ponownie z tą samą nazwą domeny. Musisz utworzyć dzierżawy B2C o nazwie innej domeny.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Link do subskrypcji dzierżawy

Musisz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, aby włączyć wszystkie funkcje B2C i opłacać opłaty za użycie. Aby dowiedzieć się więcej, przeczytaj [w tym artykule](active-directory-b2c-how-to-enable-billing.md). Jeśli nie możesz połączyć dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure, niektóre funkcje jest zablokowany, a zostanie wyświetlony komunikat ostrzegawczy ("bez subskrypcji połączone z tej dzierżawy B2C lub na potrzeby subskrypcji uwagi.") w ustawieniach usługi B2C. Należy wykonać ten krok, przed wysłaniem aplikacji w środowisku produkcyjnym.

## <a name="easy-access-to-settings"></a>Łatwy dostęp do ustawień

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Można także przejść do bloku, wprowadzając `Azure AD B2C` w **wyszukiwania zasobów** w górnej części portalu. Na liście wyników wybierz **usługi Azure AD B2C** B2C blok ustawień dostępu do.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji B2C w dzierżawcy usługi B2C](active-directory-b2c-app-registration.md)