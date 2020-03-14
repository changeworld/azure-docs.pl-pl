---
title: Dodaj domenę niestandardową — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania domeny niestandardowej za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262154"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Dodawanie niestandardowej nazwy domeny za pomocą portalu Azure Active Directory

Każda nowa dzierżawa usługi Azure AD zawiera początkową nazwę domeny, *\<nazwa_domeny >. onmicrosoft. com*. Nie można zmienić ani usunąć początkowej nazwy domeny, ale można dodać nazwy swojej organizacji. Dodawanie niestandardowych nazw domen ułatwia tworzenie nazw użytkowników, które są znane użytkownikom, takich jak *alain\@contoso.com*.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było dodać niestandardową nazwę domeny, należy utworzyć nazwę domeny przy użyciu rejestratora domen. W przypadku akredytowanego rejestratora domen zobacz [rejestratory akredytowane przez ICANN](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Tworzenie katalogu w usłudze Azure AD

Po otrzymaniu nazwy domeny, można utworzyć pierwszy katalog usługi Azure AD. Zaloguj się do Azure Portal dla katalogu przy użyciu konta z rolą **właściciela** dla subskrypcji.

Utwórz nowy katalog, wykonując kroki opisane w temacie [Tworzenie nowej dzierżawy dla organizacji](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Osoba, która tworzy dzierżawy jest automatycznie administratorem globalnym dla tej dzierżawy. Administrator globalny może dodać dodatkowych administratorów do dzierżawy.

Aby uzyskać więcej informacji na temat ról subskrypcji, zobacz [role RBAC platformy Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Jeśli planujesz sfederować lokalnego systemu Windows Server za pomocą usługi Azure AD, musisz wybrać opcję **planuję skonfigurować tę domenę na potrzeby logowania jednokrotnego przy użyciu Active Directory lokalnej** po uruchomieniu narzędzia Azure AD Connect w celu zsynchronizowania katalogów.
>
> Należy również zarejestrować tę samą nazwę domeny, która została wybrana do sfederowania z katalogiem lokalnym w kroku **Domena usługi Azure AD** w kreatorze. Aby zobaczyć, jak wygląda Instalator, zobacz temat [Weryfikowanie domeny usługi Azure AD wybranej dla Federacji](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Jeśli nie masz narzędzia Azure AD Connect, możesz [je pobrać w tym miejscu](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Dodawanie niestandardowej nazwy domeny do usługi Azure AD

Po utworzeniu katalogu, możesz dodać niestandardową nazwę domeny.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

1. Wyszukaj i wybierz *Azure Active Directory* z dowolnej strony. Następnie wybierz pozycję **niestandardowe nazwy domen** > **Dodaj domenę niestandardową**.

    ![Strona niestandardowe nazwy domen z pokazaną opcją Dodaj domenę niestandardową](media/add-custom-domain/add-custom-domain.png)

1. W polu **nazwa domeny niestandardowej**wprowadź nową nazwę organizacji, w tym przykładzie *contoso.com*. Wybierz opcję **Add domain** (Dodaj domenę).

    ![Strona niestandardowe nazwy domen, z dodawaniem strony domeny niestandardowej](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Do poprawnego działania należy dołączyć *. com*, *.NET*lub inne rozszerzenie najwyższego poziomu.

    Dodaliśmy niezweryfikowaną domenę. Zostanie wyświetlona strona **contoso.com** przedstawiająca informacje DNS. Zapisz te informacje. Będzie on potrzebny później do utworzenia rekordu TXT w celu skonfigurowania systemu DNS.

    ![Strona firmy Contoso z informacjami o wpis DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Dodaj informacje DNS do rejestratora domen

Po dodaniu niestandardowej nazwy domeny do usługi Azure AD, musisz powrócić do rejestratora domen i Dodaj informacje o usłudze Azure AD DNS z skopiowany plik TXT. Tworzenie tego rekordu TXT dla domeny weryfikuje własność nazwy domeny.

Wróć do rejestratora domen i Utwórz nowy rekord TXT dla domeny na podstawie skopiowanych informacji DNS. Ustaw wartość czasu wygaśnięcia (TTL) na 3600 sekund (60 minut), a następnie Zapisz rekord.

>[!IMPORTANT]
>Możesz zarejestrować dowolną liczbę nazw domeny. Jednak każda domena pobiera swój własny rekord TXT z usługi Azure AD. Należy zachować ostrożność w przypadku wprowadzenia informacji o pliku TXT w rejestratorze domeny. Jeśli wprowadzisz nieprawidłowe lub zduplikowane informacje, należy poczekać, aż upłynie limit czasu (60 minut), zanim będzie można spróbować ponownie.

## <a name="verify-your-custom-domain-name"></a>Sprawdź nazwę domeny niestandardowej

Po zarejestrowaniu niestandardowej nazwy domeny upewnij się, że jest ona prawidłowa w usłudze Azure AD. Propagacja z rejestratora domen do usługi Azure AD może być chwilowo lub może potrwać kilka dni, w zależności od rejestratora domen.

Aby zweryfikować niestandardową nazwę domeny, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

1. Wyszukaj i wybierz *Azure Active Directory* z dowolnej strony, a następnie wybierz pozycję **niestandardowe nazwy domen**.

1. W obszarze **niestandardowe nazwy domen**wybierz niestandardową nazwę domeny. W tym przykładzie wybierz pozycję **contoso.com**.

    ![Firma Fabrikam - niestandardowe strony nazwy domeny, z wyróżnioną pozycją firmy contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na stronie **contoso.com** wybierz pozycję **Weryfikuj** , aby upewnić się, że domena niestandardowa jest prawidłowo zarejestrowana i jest prawidłowa dla usługi Azure AD.

    ![Strona firmy Contoso z informacji wpis DNS i przycisku Weryfikuj](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Po zweryfikowaniu nazwy domeny niestandardowej można usunąć plik TXT lub MX weryfikacji.

## <a name="common-verification-issues"></a>Typowe problemy weryfikacji

Jeśli usługa Azure AD nie można zweryfikować niestandardowej nazwy domeny, należy spróbować wykonać następujące czynności:

- **Zaczekaj co najmniej godzinę i spróbuj ponownie**. Rekordy DNS muszą zostać poddane propagacji, aby usługa Azure AD mogła zweryfikować domenę. Ten proces może potrwać godzinę lub dłużej.

- **Upewnij się, że rekord DNS jest prawidłowy.** Wróć do lokacji rejestratora nazw domen. Upewnij się, że wpis istnieje i że jest zgodny z informacjami o wpisie DNS dostarczonymi przez usługę Azure AD.

  Jeśli nie możesz zaktualizować rekordu w witrynie rejestratora, Udostępnij wpis osobie, która ma uprawnienia do dodawania wpisu i sprawdź, czy jest poprawna.

- **Upewnij się, że nazwa domeny nie jest już używana w innym katalogu.** Nazwę domeny można zweryfikować tylko w jednym katalogu. Jeśli nazwa domeny jest obecnie weryfikowana w innym katalogu, nie można jej zweryfikować w nowym katalogu. Aby rozwiązać ten problem duplikatów, należy usunąć nazwę domeny z katalogu stare. Aby uzyskać więcej informacji na temat usuwania nazw domen, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).

- **Upewnij się, że nie masz żadnych niezarządzanych dzierżawców Power BI.** Jeśli użytkownicy aktywowano usługi Power BI za pośrednictwem samoobsługowego tworzenia nowego konta, a utworzona niezarządzana dzierżawa dla Twojej organizacji, należy wykonać nad zarządzaniem jako administrator wewnętrzny lub zewnętrzny przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz temat [Take over an unmanaged directory as administrator in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md) (Przejmowanie niezarządzanego katalogu jako administrator w usłudze Azure Active Directory).

## <a name="next-steps"></a>Następne kroki

- Dodawanie innego administratora globalnego do katalogu. Aby uzyskać więcej informacji, zobacz [Jak przypisać role i administratorów](active-directory-users-assign-role-azure-portal.md).

- Dodaj użytkowników do domeny. Aby uzyskać więcej informacji, zobacz [jak dodawać i usuwać użytkowników](add-users-azure-active-directory.md).

- Zarządzanie z informacjami dotyczącymi nazwy domeny w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen](../users-groups-roles/domains-manage.md).

- Jeśli masz lokalne wersje systemu Windows Server, które mają być używane razem z Azure Active Directory, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../connect/active-directory-aadconnect.md).
