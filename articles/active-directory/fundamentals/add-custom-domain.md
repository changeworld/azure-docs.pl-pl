---
title: Jak dodać domenę niestandardową do usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać domenę niestandardową przy użyciu portalu Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: lizross
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: b33f2e809ae5758e41f7a76680347b9487f3f461
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735340"
---
# <a name="how-to-add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Porady: Dodawanie niestandardowej nazwy domeny za pomocą portalu Azure Active Directory
Każdej nowej usługi Azure AD dzierżawy, który jest dostarczany z początkową nazwę domeny, *nazwa_domeny*. onmicrosoft.com. Nie można zmienić ani usunąć początkowej nazwy domeny, ale nazwy organizacji może dodać do listy. Dodawanie niestandardowych nazw domen, ułatwia utworzenie nazwy użytkownika, które są znane użytkownikom, takie jak _alain@contoso.com_.

>[!Note]
>Należy powtórzyć cały proces, od początku do końca dla każdego z niestandardowymi nazwami domen.

## <a name="add-a-custom-domain-name"></a>Dodawanie niestandardowej nazwy domeny
Najpierw należy dodać nazwę domeny niestandardowej do dzierżawy usługi Azure AD.

### <a name="to-add-a-custom-domain-name"></a>Aby dodać niestandardową nazwę domeny
1. Zaloguj się do [portalu usługi Azure AD](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **niestandardowe nazwy domen**, a następnie wybierz pozycję **Dodaj domenę niestandardową**.

    ![Firma Fabrikam - niestandardowego bloku nazwy domeny, z podświetloną opcją domeny niestandardowej Dodaj](media/add-custom-domain/add-custom-domain.png)

3. Wpisz nową nazwę domeny firmowej na **niestandardowej nazwy domeny** pole (na przykład _contoso.com_), a następnie wybierz pozycję **Dodaj domenę**.

    >[!Important]
    >Musi zawierać rozszerzenie .com, .net lub inne rozszerzenie najwyższego poziomu dla tego, aby zapewnić prawidłowe działanie.

    ![Firma Fabrikam - bloku nazwy domen niestandardowych, z wyróżnionym przyciskiem domeny Dodaj](media/add-custom-domain/add-custom-domain-blade.png)

4. Skopiuj informacje o wpis DNS z **Contoso** bloku.

    ![Blok informacji wpis DNS firmy Contoso](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-domain-name-with-a-domain-name-registrar"></a>Dodawanie nazwy domeny w rejestratorze nazw domen
Następnie należy zaktualizować plik strefy DNS dla nowej domeny niestandardowej. Możesz użyć [stref DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) dla subskrypcji platformy Azure, usługi Office 365 i zewnętrznych rekordów DNS, można także dodać nowy wpis DNS przy użyciu różnych rejestratora DNS (na przykład [InterNIC](https://go.microsoft.com/fwlink/p/?LinkId=402770)).

### <a name="to-add-your-domain-name"></a>Aby dodać nazwę domeny 
1. Zaloguj się do rejestratora nazw domen dla domeny niestandardowej. Jeśli nie masz odpowiednich uprawnień, aby zaktualizować swój wpis, należy skontaktować się z osobą z takimi uprawnieniami.

2. Po zaktualizowaniu wpisu DNS u rejestratora pliku strefy DNS należy zaktualizować przy użyciu informacji dostarczonych przez usługę Azure AD.

    >[!Note]
    >Wpis DNS nie zmienia się, jak działa Twoje routing poczty lub hosting sieci web.

## <a name="verify-your-custom-domain-name"></a>Sprawdź nazwę domeny niestandardowej
Po zarejestrowaniu nazwy domeny niestandardowej, może potrwać kilka sekund do kilku godzin, zanim propaguje informacje DNS, do której usługa Azure AD mogli je zobaczyć jako prawidłowy.

### <a name="to-verify-your-custom-domain-name"></a>Aby zweryfikować niestandardowej nazwy domeny
1. Zaloguj się do [portalu usługi Azure AD](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **niestandardowe nazwy domen**.

3. Na **Fabrikam - niestandardowe nazwy domen** bloku, wybierz nazwę domeny niestandardowej **Contoso**.

    ![Firma Fabrikam - niestandardowego bloku nazwy domeny, z wyróżnioną pozycją firmy contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

4. Na **Contoso** bloku wybierz **Sprawdź** aby upewnić się, domena niestandardowa jest poprawnie zarejestrowany i jest prawidłowy dla usługi Azure AD.

    ![Contoso bloku z informacjami wpis DNS i przycisku Weryfikuj](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

### <a name="common-verification-issues"></a>Typowe problemy weryfikacji
Jeśli usługa Azure AD nie można zweryfikować niestandardowej nazwy domeny, należy spróbować wykonać następujące czynności:
- **Poczekaj co najmniej godzinę i spróbuj ponownie**. Rekordy DNS należy poddane propagacji, aby usługa Azure AD sprawdzić, czy domena i ten proces może potrwać godzinę lub dłużej.

- **Upewnij się, że rekord DNS jest poprawna.** Wróć do witryny rejestratora nazwy domeny i upewnij się, zapis, czy istnieje i czy jest on zgodny DSN informacje podane przez usługę Azure AD.

    Jeśli nie można zaktualizować rekord w witrynie rejestratora, możesz udostępniać wpis z osobą, która ma odpowiednich uprawnień, aby dodać wpis i sprawdź, czy są dokładne.

- **Upewnij się, że nazwa domeny nie jest jeszcze w innym katalogu.** Nazwy domeny może zostać zweryfikowana tylko w jednym katalogu, co oznacza, że jeśli nazwa domeny jest obecnie weryfikowana w innym katalogu, nie można również można zweryfikować w nowym katalogu. Aby rozwiązać ten problem duplikatów, należy usunąć nazwę domeny z katalogu stare. Aby uzyskać więcej informacji na temat usuwania nazw domen, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).    

## <a name="next-steps"></a>Kolejne kroki
- Dodawanie użytkowników do domeny, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).

- Jeśli masz lokalne wersje systemu Windows Server, który chcesz użyć wraz z usługi Azure Active Directory, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md).