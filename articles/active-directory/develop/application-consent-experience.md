---
title: Środowisko zgody aplikacji usługi Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o środowiskach zgody usługi Azure AD, aby zobaczyć, jak można jej używać podczas zarządzania aplikacjami i tworzenia ich w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c9b449b65a8f8def9dc28a668cd9ee3671124cb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484506"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Opis środowisk zgody dla aplikacji usługi Azure AD

Dowiedz się więcej o interfejsie użytkownika zgody aplikacji usługi Azure Active Directory (Azure AD). Dzięki czemu można inteligentnie zarządzać aplikacjami dla swojej organizacji i/lub tworzyć aplikacje z bardziej bezproblemową zgodą.

## <a name="consent-and-permissions"></a>Zgoda i uprawnienia

Zgoda jest procesem udzielania przez użytkownika autoryzacji aplikacji w celu uzyskania dostępu do chronionych zasobów w ich imieniu. Administrator lub użytkownik może zostać poproszony o zgodę na zezwolenie na dostęp do ich organizacji/danych indywidualnych.

Rzeczywiste środowisko użytkownika udzielania zgody będzie się różnić w zależności od zasad ustawionych w dzierżawie użytkownika, zakresu uprawnień użytkownika (lub roli) i typu [uprawnień wymaganych](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) przez aplikację kliencką. Oznacza to, że deweloperzy aplikacji i administratorzy dzierżawy mają pewną kontrolę nad doświadczeniem zgody. Administratorzy mają elastyczność ustawiania i wyłączania zasad w dzierżawie lub aplikacji, aby kontrolować środowisko zgody w dzierżawie. Deweloperzy aplikacji mogą dyktować, jakie typy uprawnień są wymagane i czy chcą prowadzić użytkowników przez przepływ zgody użytkownika lub przepływ zgody administratora.

- **Przepływ zgody użytkownika** jest, gdy deweloper aplikacji kieruje użytkowników do punktu końcowego autoryzacji z zamiarem rejestrowania zgody tylko dla bieżącego użytkownika.
- **Przepływ zgody administratora** jest, gdy deweloper aplikacji kieruje użytkowników do punktu końcowego zgody administratora z zamiarem rejestrowania zgody dla całej dzierżawy. Aby upewnić się, że przepływ zgody administratora działa `RequiredResourceAccess` poprawnie, deweloperzy aplikacji muszą wyświetlić listę wszystkich uprawnień we właściwości w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [Manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Elementy konstrukcyjne monitu o zgodę

Monit o zgodę ma na celu zapewnienie użytkownikom wystarczającej ilości informacji, aby ustalić, czy ufają aplikacji klienckiej, aby uzyskać dostęp do chronionych zasobów w ich imieniu. Zrozumienie bloków konstrukcyjnych pomoże użytkownikom udzielającym zgody podejmować bardziej świadome decyzje i pomoże deweloperom tworzyć lepsze środowisko użytkownika.

Poniższy diagram i tabela zawierają informacje o blokach konstrukcyjnych monitu o zgodę.

![Elementy konstrukcyjne monitu o zgodę](./media/application-consent-experience/consent_prompt.png)

| # | Składnik | Przeznaczenie |
| ----- | ----- | ----- |
| 1 | Identyfikator użytkownika | Ten identyfikator reprezentuje użytkownika, który aplikacja kliencka żąda dostępu do chronionych zasobów w imieniu. |
| 2 | Tytuł | Tytuł zmienia się w zależności od tego, czy użytkownicy przechodzą przez przepływ zgody użytkownika lub administratora. W przepływie zgody użytkownika tytuł będzie "Wymagane uprawnienia", podczas gdy w przepływie zgody administratora tytuł będzie miał dodatkowy wiersz "Zaakceptuj dla swojej organizacji". |
| 3 | Logo aplikacji | Ten obraz powinien pomóc użytkownikom mieć wizualną wskazówkę, czy ta aplikacja jest aplikacją, do którą mają mieć dostęp. Ten obraz jest dostarczany przez deweloperów aplikacji i własność tego obrazu nie jest sprawdzana. |
| 4 | Nazwa aplikacji | Ta wartość powinna informować użytkowników, która aplikacja żąda dostępu do swoich danych. Należy zauważyć, że ta nazwa jest dostarczana przez deweloperów i własność tej nazwy aplikacji nie jest sprawdzana. |
| 5 | Domena wydawcy | Ta wartość powinna zapewnić użytkownikom domenę, którą mogą ocenić pod kątem wiarygodności. Ta domena jest dostarczana przez deweloperów, a własność tej domeny wydawcy jest sprawdzana. |
| 6 | Uprawnienia | Ta lista zawiera uprawnienia wymagane przez aplikację kliencką. Użytkownicy powinni zawsze oceniać typy uprawnień żądanych, aby zrozumieć, jakie dane aplikacja kliencka będzie autoryzowana do uzyskania dostępu w ich imieniu, jeśli zaakceptują. Jako deweloper aplikacji najlepiej jest zażądać dostępu do uprawnień z najmniejszym uprawnieniem. |
| 7 | Opis uprawnień | Ta wartość jest dostarczana przez usługę ujawniającą uprawnienia. Aby wyświetlić opisy uprawnień, należy przełączyć szewron obok uprawnienia. |
| 8 | Terminy aplikacji | Niniejsze warunki zawierają linki do warunków korzystania z usługi i oświadczenia o ochronie prywatności aplikacji. Wydawca jest odpowiedzialny za nakreślenie ich zasad w warunkach korzystania z usługi. Ponadto wydawca jest odpowiedzialny za ujawnienie sposobu, w jaki wykorzystuje i udostępnia dane użytkownika w swoich oświadczeniach o ochronie prywatności. Jeśli wydawca nie udostępnia łączy do tych wartości dla aplikacji z wieloma dzierżawami, pojawi się pogrubione ostrzeżenie w wierszu zgody. |
| 9 | https://myapps.microsoft.com | Jest to łącze, w którym użytkownicy mogą przeglądać i usuwać wszystkie aplikacje firmy innej niż Microsoft, które obecnie mają dostęp do swoich danych. |

## <a name="common-consent-scenarios"></a>Typowe scenariusze zgody

Oto środowiska zgody, które użytkownik może zobaczyć w typowych scenariuszach zgody:

1. Osoby uzyskujące dostęp do aplikacji, która kieruje ich do przepływu zgody użytkownika, wymagając jednocześnie zestawu uprawnień, który mieści się w ich zakresie uprawnień.
    
    1. Administratorzy zobaczą dodatkową kontrolę nad tradycyjnym monitem o zgodę, który umożliwi im zgodę w imieniu całej dzierżawy. Formant zostanie wyłączony, więc tylko wtedy, gdy administratorzy jawnie zaznaczyć pole zgody zostaną udzielone w imieniu całej dzierżawy. Od dzisiaj to pole wyboru będzie wyświetlane tylko dla roli Administrator globalny, więc administrator chmury i administrator aplikacji nie zobaczą tego pola wyboru.

        ![Monit o wyrażenie zgody na scenariusz 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Użytkownicy zobaczą tradycyjny monit o zgodę.

        ![Monit o wyrażenie zgody dla scenariusza 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Osoby uzyskujące dostęp do aplikacji, która wymaga co najmniej jednego uprawnienia, które nie są objęte ich zakresem uprawnień.
    1. Administratorzy zobaczą ten sam monit, co 1.i pokazany powyżej.
    2. Użytkownicy będą mogli uzyskać zgodę na aplikację i będą musieli poprosić administratora o dostęp do aplikacji. 
                
        ![Monit o wyrażenie zgody dla scenariusza 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Osoby, które nawigują lub są kierowane do przepływu zgody administratora.
    1. Użytkownicy administratora zobaczą monit o zgodę administratora. Tytuł i opisy uprawnień zmienione w tym wierszu, zmiany podkreślają fakt, że zaakceptowanie tego monitu zapewni aplikacji dostęp do żądanych danych w imieniu całej dzierżawy.
        
        ![Monit o wyrażenie zgody dla scenariusza 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Użytkownicy niebędący administratorami zobaczą ten sam ekran, co pokazano powyżej w wersji 2.ii.

## <a name="next-steps"></a>Następne kroki
- Uzyskaj omówienie krok po kroku, [w jaki sposób platforma zgody usługi Azure AD implementuje zgodę.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- Aby uzyskać więcej informacji, dowiedz się, [jak aplikacja z wieloma dzierżawami może używać struktury zgody](active-directory-devhowto-multi-tenant-overview.md) do implementowania zgody "użytkownik" i "administrator", obsługując bardziej zaawansowane wzorce aplikacji wielowarstwowych.
- Dowiedz [się, jak skonfigurować domenę wydawcy aplikacji](howto-configure-publisher-domain.md).
