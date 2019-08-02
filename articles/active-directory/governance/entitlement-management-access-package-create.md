---
title: Utwórz nowy pakiet dostępu w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak utworzyć nowy pakiet dostępu do zasobów, które chcesz udostępnić w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f033cf57c5a285e94372728677c91e021065fa9
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678196"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Tworzenie nowego pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pakiet dostępu umożliwia jednorazowe skonfigurowanie zasobów i zasad, które automatycznie zarządzają dostępem do czasu życia pakietu dostępu. W tym artykule opisano sposób tworzenia nowego pakietu dostępu.

## <a name="overview"></a>Przegląd

Wszystkie pakiety dostępu muszą być umieszczone w kontenerze o nazwie wykaz. Katalog definiuje zasoby, które można dodać do pakietu dostępu. Jeśli nie określisz katalogu, pakiet dostępu zostanie umieszczony w katalogu ogólnym. Obecnie nie można przenieść istniejącego pakietu dostępu do innego katalogu.

Wszystkie pakiety dostępu muszą mieć co najmniej jedną zasadę. Zasady określają, kto może zażądać pakietu dostępu, a także ustawień zatwierdzania i wygasania. Podczas tworzenia nowego pakietu dostępu można utworzyć zasady początkowe dla użytkowników w katalogu, w przypadku użytkowników, którzy nie są w katalogu, tylko do przypisywania bezpośrednio dla administratorów lub wybrać opcję tworzenia zasad później.

Na poniższym diagramie przedstawiono proces wysokiego poziomu służący do tworzenia nowego pakietu dostępu.

![Tworzenie procesu pakietu dostępu](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Uruchom nowy pakiet dostępu

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.

    ![Zarządzanie prawami w Azure Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Kliknij pozycję **nowy pakiet dostępu**.

## <a name="basics"></a>Ustawienia podstawowe

Na karcie **podstawowe** można nadać pakietowi dostępu nazwę i określić katalog, w którym ma zostać utworzony pakiet dostępu.

1. Wprowadź nazwę wyświetlaną i Opis pakietu dostępu. Użytkownicy będą widzieć te informacje podczas przesyłania żądania dla pakietu dostępu.

1. Z listy rozwijanej **wykaz** wybierz katalog, w którym chcesz utworzyć pakiet dostępu. Na przykład może istnieć właściciel katalogu, który zarządza wszystkimi zasobami marketingowymi, które mogą być żądane. W takim przypadku można wybrać Katalog Marketing.

    Zostaną wyświetlone tylko wykazy, do których masz uprawnienia do tworzenia pakietów dostępu w programie. Aby utworzyć pakiet dostępu w istniejącym wykazie, musisz być administratorem globalnym, administratorem użytkowników, właścicielem katalogu w tym wykazie lub uzyskać dostęp do Menedżera pakietów w tym katalogu.

    ![Pakiet dostępu — podstawy](./media/entitlement-management-access-package-create/basics.png)

    Jeśli jesteś administratorem globalnym lub administratorem użytkownika i chcesz utworzyć pakiet dostępu w nowym wykazie, którego nie ma na liście, kliknij przycisk **Utwórz nowy**. Wprowadź nazwę wykazu i opis, a następnie kliknij przycisk **Utwórz**.

    Tworzony pakiet dostępu i wszystkie zawarte w nim zasoby zostaną dodane do nowego katalogu. Możesz również później dodać dodatkowych właścicieli katalogu.


1. Kliknij przycisk **Dalej**.

## <a name="resource-roles"></a>Role zasobów

Na karcie **role zasobów** Wybierz zasoby do uwzględnienia w pakiecie dostępu.  Użytkownicy, którzy żądają i otrzymują pakiet dostępu, otrzymają wszystkie role zasobów w pakiecie dostępu.

1. Kliknij typ zasobu, który chcesz dodać (**grupy**, **aplikacje**lub **witryny programu SharePoint**).

1. W wyświetlonym okienku wybierz pozycję co najmniej jeden zasób z listy.

    ![Dostęp do ról zasobów](./media/entitlement-management-access-package-create/resource-roles.png)

    Jeśli tworzysz pakiet dostępu w wykazie ogólnym lub w nowym katalogu, będziesz mieć możliwość wybrania dowolnego zasobu z katalogu, którego jesteś członkiem. Musisz być co najmniej administratorem globalnym, administratorem użytkownika lub autorem katalogu.

    Jeśli tworzysz pakiet dostępu w istniejącym wykazie, możesz wybrać dowolny zasób, który znajduje się już w katalogu bez jego właściciela.

    Jeśli jesteś administratorem globalnym, administratorem lub właścicielem katalogu, masz dodatkową możliwość wyboru zasobów, których jesteś właścicielem, które nie znajdują się jeszcze w wykazie. W przypadku wybrania zasobów, które nie są obecnie w wybranym wykazie, te zasoby zostaną również dodane do wykazu dla innych administratorów wykazu do tworzenia pakietów dostępu za pomocą programu. Jeśli chcesz tylko wybrać zasoby, które znajdują się obecnie w wybranym wykazie, zaznacz pole wyboru **Zobacz tylko** w górnej części strony wybieranie.

1. Po wybraniu zasobów na liście **rola** wybierz rolę, do której użytkownicy mają być przypisani.

    ![Dostęp do pakietu — wybór roli zasobów](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kliknij przycisk **Dalej**.

## <a name="policy"></a>Zasady

Na karcie **zasady** Utwórz pierwsze zasady, aby określić, kto może zażądać pakietu dostępu, a także ustawienia zatwierdzenia i wygaśnięcia. Później możesz utworzyć więcej zasad, aby umożliwić dodatkowym grupom użytkowników zażądanie pakietu dostępu przy użyciu własnych ustawień zatwierdzenia i wygaśnięcia. Możesz również utworzyć zasady później.

1. Ustaw przełącznik **Utwórz pierwsze zasady** na **teraz** lub **później**.

    ![Dostęp do pakietu — zasady](./media/entitlement-management-access-package-create/policy.png)

1. W przypadku wybrania opcji **później**przejdź do sekcji [Recenzja + tworzenie](#review--create) , aby utworzyć pakiet dostępu.

1. Jeśli wybierzesz opcję **teraz**, wykonaj czynności opisane w jednej z poniższych sekcji zasad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Przeglądanie + tworzenie

Na karcie **Przegląd i tworzenie** możesz przejrzeć ustawienia i sprawdzić, czy występują błędy walidacji.

1. Przejrzyj ustawienia pakietu dostępu

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć pakiet dostępu.

    Nowy pakiet dostępu zostanie wyświetlony na liście pakietów dostępu.

## <a name="next-steps"></a>Kolejne kroki

- [Edytowanie istniejącego pakietu dostępu i zarządzanie nim](entitlement-management-access-package-edit.md)
- [Dodawanie właściciela katalogu lub Menedżera pakietów programu Access](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Tworzenie wykazu i zarządzanie nim](entitlement-management-catalog-create.md)
