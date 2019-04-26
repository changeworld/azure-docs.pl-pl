---
title: Informacje o wyrażenie zgody aplikacji usługi Azure AD, wystąpi | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat usługi Azure AD zgody środowiska, aby zobaczyć, jak można ją podczas zarządzania i programowania aplikacji w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7de892143e1c69953cc60429ea0d24df194f0df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300286"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Opis środowisk zgody dla aplikacji usługi Azure AD

Dowiedz się więcej na temat aplikacji Azure Active Directory (Azure AD) zgoda środowisko użytkownika. Dlatego inteligentnie Zarządzanie aplikacjami w swojej organizacji lub Twórz aplikacje za pomocą aby usprawnić środowisko zgody.

## <a name="consent-and-permissions"></a>Uprawnienia i zgody

Zgoda jest proces udzielania autoryzacji do aplikacji w celu dostępu do chronionych zasobów w imieniu użytkownika. Administrator lub użytkownik można poprosić o ich zgodę zezwolić na dostęp do danych organizacji/poszczególnych użytkowników.

Rzeczywiste środowisko udzielania zgody będą się różnić w zależności od zasad zabezpieczeń ustawionych w dzierżawie użytkownika, zakresu użytkownika urzędu (lub rolach) i typ [uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) żądanego przez aplikację klienta. Oznacza to, że niektóre kontrolę nad środowiskiem zgody posiadanych przez deweloperów aplikacji i administratorów dzierżawy. Administratorzy mają elastyczność i wyłączanie zasad na dzierżawy lub aplikacji do sterowania środowisko zgody w ramach ich dzierżawy. Deweloperzy aplikacji mogą określają, jakie uprawnienia są żądane i chcą prowadzą użytkowników przez użytkownika wyraża zgodę przepływu lub przepływu wyrażania zgody administratora.

- **Przepływu wyrażania zgody użytkownika** jest, gdy deweloper aplikacji kieruje użytkowników do punktu końcowego autoryzacji z zamiarem rekordów wyrażania zgody dla bieżącego użytkownika.
- **Przepływu wyrażania zgody administratora** jest, gdy deweloper aplikacji kieruje użytkowników do administratora zgody punktu końcowego z zamiarem rekordów zgody na całej dzierżawie. Aby zapewnić przepływu wyrażania zgody administratora działa poprawnie, deweloperzy aplikacji musi zawierać wszystkie uprawnienia w `RequiredResourceAccess` właściwości w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Bloki konstrukcyjne monit o wyrażenie zgody

Monit o wyrażenie zgody jest przeznaczony do upewnij się, że użytkownicy mają wystarczająco dużo informacji, aby określić, jeśli są zaufane aplikacji klienckiej dostęp do chronionych zasobów w ich imieniu. Dotyczącej bloków konstrukcyjnych pomoże użytkownikom udzielania zgody upewnij, więcej informacji decyzji i ułatwić deweloperom tworzenie lepszych rozwiązań.

Poniższy diagram i tabeli, podaj informacje na temat bloków konstrukcyjnych monit o wyrażenie zgody.

![Bloki konstrukcyjne monit o wyrażenie zgody](./media/application-consent-experience/consent_prompt.png)

| # | Składnik | Przeznaczenie |
| ----- | ----- | ----- |
| 1 | Identyfikator użytkownika | Ten identyfikator reprezentuje użytkownika, który aplikacja kliencka żąda dostępu do chronionych zasobów w imieniu osób. |
| 2 | Tytuł | Tytuł zmienia się zależnie od tego, czy użytkownicy są poddawane przepływu wyrażania zgody użytkownika lub administratora. W usłudze flow zgody użytkownika tytuł będzie "Żądane uprawnienia" podczas przepływu wyrażania zgody administratora tytuł będzie miał dodatkowy wiersz "Akceptuj dla Twojej organizacji". |
| 3 | Logo aplikacji | Ten obraz Ci użytkownicy wizualną od tego, czy ta aplikacja jest aplikacją ma dostęp. Ten obraz jest dostarczany przez deweloperów aplikacji i własność ten obraz nie jest zweryfikowany. |
| 4 | Nazwa aplikacji | Tę wartość należy poinformować użytkowników, która aplikacja żąda dostępu do swoich danych. Należy pamiętać, ta nazwa jest świadczona przez deweloperów i własności to nazwa aplikacji nie jest zweryfikowany. |
| 5 | Domena wydawcy | Ta wartość powinna udostępniają użytkownikom domeny, które mogą być w stanie oceniająca wiarygodności. Ta domena jest świadczona przez deweloperów i zweryfikowaniu własności tej domeny wydawcy. |
| 6 | Uprawnienia | Ta lista zawiera wymagane przez aplikację kliencką uprawnienia. Użytkownicy zawsze należy ocenić typy Aby zrozumieć, jakie dane, aplikacja kliencka będzie autoryzowana do dostępu w ich imieniu po ich zaakceptowaniu wymagane uprawnienia. Jako Deweloper aplikacji najlepiej jest żądanie dostępu do uprawnień za pomocą najniższych uprawnień. |
| 7 | Opis uprawnienia | Ta wartość jest udostępniany przez usługę ujawnienia uprawnień. Aby wyświetlić opisy uprawnień, możesz przełączyć cudzysłów ostrokątny obok uprawnienia. |
| 8 | Warunki aplikacji | Niniejsze postanowienia zawierają linki do warunków usługi i zasady zachowania poufności aplikacji. Wydawca jest odpowiedzialny za tworzenie konspektu ich zestawów reguł w jej warunkach użytkowania usługi. Ponadto wydawca jest odpowiedzialny za ujawnieniu sposób ich używania i udostępniania danych użytkowników w ich zachowania poufności informacji. Jeśli wydawca nie udostępnia łącza do tych wartości dla aplikacji wielodostępnych, będzie pogrubiony ostrzeżenia na monit o wyrażenie zgody. |
| 9 | https://myapps.microsoft.com | Jest to link, w którym użytkownicy mogą przejrzeć i usunąć wszystkie aplikacje firm innych niż Microsoft, które aktualnie mają dostęp do swoich danych. |

## <a name="common-consent-scenarios"></a>Typowe scenariusze zgody

Poniżej przedstawiono procesy zgody, które użytkownik może zostać wyświetlony w typowych scenariuszach zgody:

1. Osobom dostęp do aplikacji z hiperlinkiem do użytkownika wyraża zgodę przepływu podczas wymagające zestawu uprawnień, który znajduje się w ich zakresie urzędu.
    
    1. Administratorzy zobaczą dodatkowej kontroli w wierszu polecenia z tradycyjnych zgody, który umożliwi im zgody w imieniu całej dzierżawie. Formant zostanie przywrócona wartość domyślna jest wyłączany, więc tylko gdy Administratorzy jawnie Sprawdź, czy pole zgody udzielone w imieniu całej dzierżawie. Obecnie to pole wyboru będzie wyświetlana tylko dla roli administratora globalnego, więc aplikacja i administratora chmury nie będą widzieć tego pola wyboru.

        ![Monit o wyrażenie zgody na scenariusz 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Użytkownicy zobaczą monit o tradycyjnych wyrażenie zgody.

        ![Monit o wyrażenie zgody na scenariusz 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Uzyskiwanie dostępu do aplikacji, która wymaga co najmniej jedno uprawnienie, który znajduje się poza ich zakresem urzędu użytkowników indywidualnych.
    1. Administratorzy zobaczą tym samym wierszu jako 1.i przedstawionych powyżej.
    2. Użytkownikom zostanie zablokowany udzielania zgody aplikacji, a będą powiadamiani poprosić swojego administratora o dostęp do aplikacji. 
                
        ![Monit o wyrażenie zgody na scenariusz 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Osoby, które przejść lub są kierowane do administratora zgody przepływu.
    1. Administratorzy zobaczą monit o wyrażenie zgody administratora. Tytuł i opis uprawnienia zmieniony na ten monit, Wyróżnij zmiany fakt, że akceptując ten monit spowoduje przyznanie aplikacji dostęp do żądanych danych w imieniu całej dzierżawie.
        
        ![Monit o wyrażenie zgody na scenariusz 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Użytkownicy inni niż administratorzy zobaczą tym samym ekranie jako ii 2. przedstawionych powyżej.

## <a name="next-steps"></a>Kolejne kroki
- Szczegółowe omówienie [jak platformy wyrażania zgody w usłudze Azure AD implementuje zgody](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Aby uzyskać bardziej szczegółowe informacje, Dowiedz się, [użycia platformy wyrażania zgody aplikacji z wieloma dzierżawami](active-directory-devhowto-multi-tenant-overview.md) do zaimplementowania "user" i "admin" zgody, obsługa więcej zaawansowanych wzorce aplikacji wielowarstwowych.
- Dowiedz się, [sposobu konfigurowania domena wydawcy aplikacji](howto-configure-publisher-domain.md).
