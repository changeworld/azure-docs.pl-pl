---
title: Dodaj domenę niestandardową — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania domeny niestandardowej za pomocą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb29fb5ef2e755ff456ad177b66349792b2fa21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248418"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Dodawanie niestandardowej nazwy domeny za pomocą portalu Azure Active Directory
Każdej nowej usługi Azure AD dzierżawy, który jest dostarczany z początkową nazwę domeny, *nazwa_domeny*. onmicrosoft.com. Nie można zmienić ani usunąć początkowej nazwy domeny, ale nazwy organizacji może dodać do listy. Dodawanie niestandardowych nazw domen, ułatwia utworzenie nazwy użytkownika, które są znane użytkownikom, takie jak *alain\@contoso.com*.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed dodaniem niestandardowej nazwy domeny, należy utworzyć nazwy domeny za pomocą rejestratora domen. Aby rejestratora domen akredytowanych, zobacz [rejestratorów ICANN-Accredited](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Tworzenie katalogu w usłudze Azure AD
Po otrzymaniu nazwy domeny, można utworzyć pierwszy katalog usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) dla katalogu, przy użyciu konta z **właściciela** roli dla subskrypcji, a następnie wybierz **usługi Azure Active Directory**. Aby uzyskać więcej informacji na temat ról subskrypcji, zobacz [ról administratora subskrypcji klasyczne, ról RBAC platformy Azure i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

    ![Azure portal przedstawiający ekran opcji usługi Azure AD](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

    >[!TIP]
    > Jeśli zamierzasz sfederować lokalną usługę AD systemu Windows Server z usługą Azure AD, zaznacz pole wyboru **Planuję skonfigurować tę domenę do logowania jednokrotnego przy użyciu lokalnej usługi Active Directory** podczas uruchamiania narzędzia Azure AD Connect, aby zsynchronizować katalogi. Należy również zarejestrować tę samą nazwę domeny, która została wybrana do sfederowania z katalogiem lokalnym w kroku **Domena usługi Azure AD** w kreatorze. Wygląd tego kroku w Kreatorze można zobaczyć w [tych instrukcjach](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Jeśli nie masz narzędzia Azure AD Connect, możesz [pobrać jej tutaj](https://go.microsoft.com/fwlink/?LinkId=615771).

2. Tworzenie nowego katalogu, wykonując kroki opisane w [utworzyć nową dzierżawę dla organizacji](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

    >[!Important]
    >Osoba, która tworzy dzierżawy jest automatycznie administratorem globalnym dla tej dzierżawy. Administrator globalny może dodać dodatkowych administratorów do dzierżawy.

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Dodawanie niestandardowej nazwy domeny do usługi Azure AD
Po utworzeniu katalogu, możesz dodać niestandardową nazwę domeny.

1. Wybierz **niestandardowe nazwy domen**, a następnie wybierz pozycję **Dodaj domenę niestandardową**.

    ![Strona nazwy domeny niestandardowej, za pomocą Dodaj domenę niestandardową pokazano](media/add-custom-domain/add-custom-domain.png)

2. Wpisz nową nazwę domeny w organizacji do **niestandardowej nazwy domeny** pole (na przykład _contoso.com_), a następnie wybierz pozycję **Dodaj domenę**.

    Niezweryfikowanej domeny jest dodawany i **Contoso** zostanie wyświetlona strona zawierająca swoje informacje DNS.

    >[!Important]
    >Musi zawierać rozszerzenie .com, .net lub inne rozszerzenie najwyższego poziomu dla tego, aby zapewnić prawidłowe działanie.

    ![Strona nazwy domeny niestandardowej, za pomocą Dodaj domenę niestandardową stronę](media/add-custom-domain/add-custom-domain-blade.png)

4. Kopiuj informacje DNS z **Contoso** strony. Na przykład MS = ms64983159.

    ![Strona firmy Contoso z informacjami o wpis DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Dodaj informacje DNS do rejestratora domen
Po dodaniu niestandardowej nazwy domeny do usługi Azure AD, musisz powrócić do rejestratora domen i Dodaj informacje o usłudze Azure AD DNS z skopiowany plik TXT. Tworzenie tego TXT rekordu dla domeny "sprawdza" własności nazwy domeny.

-  Wróć do rejestratora domen, Utwórz nowy rekord TXT dla domeny na podstawie informacji DNS skopiowany, ustaw **TTL** (czas wygaśnięcia) do 3600 sekund (60 minut), a następnie zapisz informacje.

    >[!Important]
    >Możesz zarejestrować dowolną liczbę nazw domeny. Jednak każda domena pobiera swój własny rekord TXT z usługi Azure AD. Należy zachować ostrożność podczas wprowadzania danych pliku TXT u rejestratora domen. Jeśli wprowadź nieprawidłowa lub zduplikowana informacji przez pomyłkę, należy poczekać, aż wartość TTL upłynie limit czasu (60 minut), aby spróbować ponownie.

## <a name="verify-your-custom-domain-name"></a>Sprawdź nazwę domeny niestandardowej
Po zarejestrowaniu niestandardową nazwę domeny, należy upewnić się, że jest on prawidłowy w usłudze Azure AD. Propagacja z Rejestratora domen do usługi Azure AD może być natychmiastowe lub może potrwać nawet kilka dni, w zależności od rejestratora domen.

### <a name="to-verify-your-custom-domain-name"></a>Aby zweryfikować niestandardowej nazwy domeny
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **niestandardowe nazwy domen**.

3. Na **Fabrikam - niestandardowe nazwy domen** wybierz nazwę domeny niestandardowej **Contoso**.

    ![Firma Fabrikam - niestandardowe strony nazwy domeny, z wyróżnioną pozycją firmy contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

4. Na **Contoso** wybierz opcję **Sprawdź** aby upewnić się, domena niestandardowa jest poprawnie zarejestrowany i jest prawidłowy dla usługi Azure AD.

    ![Strona firmy Contoso z informacji wpis DNS i przycisku Weryfikuj](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Po zweryfikowaniu niestandardową nazwę domeny, można usunąć pliku TXT lub MX weryfikacji.

## <a name="common-verification-issues"></a>Typowe problemy weryfikacji
- Jeśli usługa Azure AD nie można zweryfikować niestandardowej nazwy domeny, należy spróbować wykonać następujące czynności:
  - **Poczekaj co najmniej godzinę i spróbuj ponownie**. Rekordy DNS należy poddane propagacji, aby usługa Azure AD sprawdzić, czy domena i ten proces może potrwać godzinę lub dłużej.

  - **Upewnij się, że rekord DNS jest poprawna.** Wróć do witryny rejestratora nazwy domeny i upewnij się, zapis, czy istnieje i czy jest on zgodny informacje wpisu DNS, które są dostarczane przez usługę Azure AD.

    Jeśli nie można zaktualizować rekord w witrynie rejestratora, możesz udostępniać wpis z osobą, która ma odpowiednich uprawnień, aby dodać wpis i sprawdź, czy są dokładne.

- **Upewnij się, że nazwa domeny nie jest już używana w innym katalogu.** Nazwy domeny może zostać zweryfikowana tylko w jednym katalogu, co oznacza, że jeśli nazwa domeny jest obecnie weryfikowana w innym katalogu, nie można również można zweryfikować w nowym katalogu. Aby rozwiązać ten problem duplikatów, należy usunąć nazwę domeny z katalogu stare. Aby uzyskać więcej informacji na temat usuwania nazw domen, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).

- **Upewnij się, że nie ma żadnych niezarządzanych dzierżawach usługi Power BI.** Jeśli użytkownicy aktywowano usługi Power BI za pośrednictwem samoobsługowego tworzenia nowego konta, a utworzona niezarządzana dzierżawa dla Twojej organizacji, należy wykonać nad zarządzaniem jako administrator wewnętrzny lub zewnętrzny przy użyciu programu PowerShell. Aby dowiedzieć się więcej na temat przejmowanie katalogu niezarządzanego, zobacz [przejąć niezarządzanego katalogu jako administrator usługi Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Kolejne kroki

- Dodawanie innego administratora globalnego do katalogu. Aby uzyskać więcej informacji, zobacz [jak przypisać role i Administratorzy](active-directory-users-assign-role-azure-portal.md).

- Dodawanie użytkowników do domeny, zobacz [jak dodać lub usunąć użytkowników](add-users-azure-active-directory.md).

- Zarządzanie z informacjami dotyczącymi nazwy domeny w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).

- Jeśli masz lokalne wersje systemu Windows Server, który chcesz użyć wraz z usługi Azure Active Directory, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md).
