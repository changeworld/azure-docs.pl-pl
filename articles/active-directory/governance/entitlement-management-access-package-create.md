---
title: Utwórz nowy pakiet dostępu w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak utworzyć nowy pakiet dostępu do zasobów, które chcesz udostępnić w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 312658bd653d94f3e1a32204b3c5ae4ae290558e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65832778"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Utwórz nowy pakiet dostępu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pakiet dostępu umożliwia wykonywanie jednorazowej konfiguracji zasobów i zasad, który automatycznie zarządza dostępu przez cały okres istnienia pakiet dostępu. W tym artykule opisano sposób tworzenia nowego pakietu dostępu.

## <a name="overview"></a>Omówienie

Wszystkie pakiety dostępu muszą znajdować się w kontenerze o nazwie katalogu. Wykaz określa jakie zasoby można dodawać do dostępu do pakietu. Jeśli nie określisz katalogu pakietu dostępu będzie umieszczana w katalogu ogólne. Obecnie nie można przenieść istniejący pakiet dostęp do innego katalogu.

Wszystkie pakiety dostępu musi mieć co najmniej jedne zasady. Zasady określają, kto może żądać dostępu do pakietu, a także ustawienia zatwierdzania i wygaśnięcia. Podczas tworzenia nowego pakietu dostępu, można utworzyć początkową zasad dla użytkowników w katalogu dla użytkowników spoza katalogu dla administratora tylko bezpośredniego przypisania lub możesz później utworzyć zasady.

Na poniższym diagramie przedstawiono procedurę wysokiego poziomu służącą do utworzenia nowego pakietu dostępu.

![Tworzenie procesu pakietu dostępu](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Rozpocznij nowy pakiet dostępu

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Kliknij przycisk **nowy pakiet dostępu**.

## <a name="basics"></a>Podstawy

Na **podstawy** karcie należy nazwij pakiet dostępu i określić, którego katalogu, aby utworzyć pakiet dostępu w.

1. Wprowadź nazwę wyświetlaną i Opis pakietu dostępu. Użytkownicy będą widzieć te informacje podczas przesyłania żądania dostępu do pakietu.

1. W **katalogu** listy rozwijanej, wybierz katalog ma zostać utworzona dostępu do pakietu w. Na przykład może mieć właściciela katalogu, który zarządza wszystkich zasobów marketingowych, które mogą być wymagane. W takim przypadku można wybrać marketingu katalogu.

    Widoczne są tylko katalogi ma uprawnienia do tworzenia pakietów dostępu w. Aby utworzyć pakiet dostępu w istniejącego katalogu, musi być co najmniej jeden administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów.

    ![Pakiet dostępu — podstawy](./media/entitlement-management-access-package-create/basics.png)

    Jeśli chcesz utworzyć pakiet dostępu w nowy katalog, kliknij przycisk **Utwórz nową**. Wprowadź nazwę katalogu i opis, a następnie kliknij przycisk **Utwórz**.

    Tworzenie pakietu dostępu i wszystkie zasoby zawarte w nim zostaną dodane do nowego katalogu. Ponadto automatycznie zostaniesz pierwszym właściciel katalogu. Możesz dodać dodatkowe katalogu właścicieli.

    Aby utworzyć nowy katalog, musi być co najmniej użytkownika administrator lub twórca katalogu.

1. Kliknij przycisk **Dalej**.

## <a name="resource-roles"></a>Role zasobów

Na **role zasobów** karcie Wybierz zasoby do uwzględnienia w pakiecie dostępu.

1. Kliknij typ zasobu, które chcesz dodać (**grup**, **aplikacje**, lub **witryny programu SharePoint**).

1. W wyświetlonym okienku wybierz opcję należy wybrać co najmniej jednego zasobu z listy.

    ![Pakiet dostępu — role zasobów](./media/entitlement-management-access-package-create/resource-roles.png)

    Jeśli tworzysz pakiet dostępu w katalogu ogólne lub nowy katalog, będzie można wybrać dowolnego zasobu z katalogu, którego jesteś właścicielem. Musi być co najmniej jeden administrator użytkowników lub twórca w katalogu.

    Jeśli tworzysz pakiet dostępu w istniejącego katalogu, można wybrać dowolnego zasobu, który znajduje się już w katalogu bez konieczności posiadania go.

    Jeśli jesteś administratorem użytkownika lub właściciela katalogu jest istnieje dodatkowa opcja wybierania zasobów, których jesteś właścicielem, które nie są jeszcze w wykazie. Wybranie zasobów nie jest obecnie w wybrany katalog, to te zasoby również zostaną dodane do katalogu dla innych administratorów wykazu do tworzenia pakietów dostępu. Jeśli chcesz wybrać zasoby, które są aktualnie wybrany katalog wyboru **tylko** pole wyboru w górnej części wybierz opcję przesuwanie.

1. Po wybraniu zasobów, w **roli** listy, wybierz rolę, ma być można przypisać użytkowników dla zasobu.

    ![Pakiet dostępu — Wybór roli zasobu](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kliknij przycisk **Dalej**.

## <a name="policy"></a>Zasady

Na **zasad** kartę, Utwórz pierwszą zasadę, aby określić, kto może żądać dostępu do pakietu, a także ustawienia zatwierdzania i wygaśnięcia. Później należy utworzyć zasady umożliwiające dodatkowe grupy użytkowników do żądania pakietu dostęp z własnych ustawień zatwierdzania i wygaśnięcia. Możesz również utworzyć zasady później.

1. Ustaw **Utwórz pierwszą zasadę** Przełącz, aby **teraz** lub **później**.

    ![Pakiet — zasady dostępu](./media/entitlement-management-access-package-create/policy.png)

1. Jeśli wybierzesz **później**, przejdź w dół do [przeglądu + Utwórz](#review--create) sekcję, aby utworzyć pakiet dostępu.

1. Jeśli wybierzesz **teraz**, wykonaj kroki w jednym z następujących sekcji zasady.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Przeglądanie + tworzenie

Na **Przejrzyj + Utwórz** karcie, można przejrzeć ustawienia i wyboru dla każdego błędu weryfikacji.

1. Przejrzyj ustawienia pakietu dostępu

    ![Pakiet dostępu — ustawienie zasad obsługi zasad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknij przycisk **Utwórz** do utworzenia pakietu dostępu.

    Nowy pakiet dostępu pojawia się na liście dostępu do pakietów.

## <a name="next-steps"></a>Kolejne kroki

- [Edytowanie istniejącego pakietu dostępu i zarządzanie nim](entitlement-management-access-package-edit.md)
- [Tworzenie i zarządzanie nimi katalogu](entitlement-management-catalog-create.md)
