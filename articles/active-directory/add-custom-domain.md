---
title: Dodaj niestandardową domenę do usługi Azure AD | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak dodać niestandardową domenę w usłudze Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 32e3fc2db0a88fd004f3ff5c16b96ec282606d6b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714225"
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Szybki Start: Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory

Każdy katalog usługi Azure AD jest dostarczany z początkową nazwę domeny w postaci *domainname*. onmicrosoft.com. Nie można zmienić ani usunąć z początkowej nazwy domeny, ale można dodać nazwę domeny firmowej do usługi Azure AD oraz. Twoja organizacja ma prawdopodobnie innych nazw domen używane biznesowe i użytkowników, którzy Zaloguj się przy użyciu nazwy domeny firmowej. Dodawanie niestandardowych nazw domen do usługi Azure AD umożliwia przypisywanie nazw użytkowników w katalogu, które są znajome dla użytkowników, takich jak "alice@contoso.com." zamiast "Alicja @*nazwy domeny*. onmicrosoft.com". Proces jest prosty:

1. Dodawanie niestandardowej nazwy domeny do katalogu
2. Dodawanie wpisu DNS dla nazwy domeny w rejestratorze nazw domen
3. Weryfikowanie niestandardowej nazwy domeny w usłudze Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Dodawanie niestandardowej nazwy domeny do katalogu
1. Zaloguj się do [portalu Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Po lewej stronie, wybierz **niestandardowych nazw domen**.
3. Wybierz **Dodaj domenę niestandardową**.
   
   ![Wybierz polecenie Dodaj](./media/add-custom-domain/add-custom-domain.png)
5. Na **niestandardowych nazw domen**, wprowadź nazwę domeny niestandardowej w polu, takie jak "contoso.com", a następnie wybierz **Dodawanie domeny**. Należy uwzględnić rozszerzenie .com, .net lub inne rozszerzenie najwyższego poziomu.
6. Na ***domainname*** (Nowa nazwa domeny jest tytuł), zebraniu informacji wpisu DNS do użycia w przyszłości można zweryfikować nazwy domeny niestandardowej w usłudze Azure AD.
   
   ![Pobierz informacje wpisu DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Jeśli planujesz utworzenie Federacji lokalnego serwera z systemem Windows AD z usługą Azure AD, a następnie należy wybrać **planuję skonfigurować tę domenę na potrzeby rejestracji jednokrotnej z mojej lokalnej usługi Active Directory** wyboru w celu uruchomienia narzędzia Azure AD Connect do Zsynchronizuj swoje katalogi. Należy również zarejestrować tej samej nazwy domeny, wybierz dla operacji federowania z katalogu lokalnego w **domenowych Azure AD** kroku kreatora. Wygląd tego kroku w Kreatorze można zobaczyć w [tych instrukcjach](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Jeśli nie masz narzędzia Azure AD Connect, możesz [pobrać jej tutaj](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Dodawanie wpisu DNS dla nazwy domeny w rejestratorze nazw domen
Następnym krokiem do korzystania z niestandardowej nazwy domeny w usłudze Azure AD jest zaktualizowanie pliku strefy DNS dla domeny. Usługi Azure AD można następnie sprawdź, czy organizacja jest właścicielem nazwy domeny niestandardowej. Można użyć [usługi Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) dla Azure/Office 365/zewnętrznych rekordów DNS w systemie Azure, lub Dodaj wpis DNS w [różnych rejestratora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Zaloguj się do rejestratora nazw domen dla domeny. Jeśli nie masz dostępu potrzebnego do zaktualizowania wpisu DNS, poproś osobę lub zespół, którzy mają odpowiedni dostęp, o wykonanie kroku 2 i powiadomienie Cię o jego zakończeniu.
2. Zaktualizuj plik strefy DNS dla domeny, dodając wpis DNS udostępniony przez usługę Azure AD. Ten wpis DNS nie zmienia żadnych zachowań, takich jak routing poczty lub hosting sieci Web.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Weryfikowanie niestandardowej nazwy domeny w usłudze Azure AD
Po dodaniu wpisu DNS można przystąpić do weryfikowania nazwy domeny w usłudze Azure AD. Nazwa domeny można sprawdzić tylko wtedy, gdy zostaną rozpropagowane rekordów DNS. Propagacja często zajmuje tylko kilka sekund, ale czasami może potrwać godzinę lub dłużej. Jeśli weryfikacja nie zadziała za pierwszym razem, spróbuj ponownie później.

1. Zaloguj się do [usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym dla dzierżawy.
2. Wybierz **niestandardowych nazw domen**.
3. Wybierz nazwę niezweryfikowanej domeny, który chcesz zweryfikować.
4. Sprawdź wpisy i wybierz **Sprawdź** do zakończenia weryfikacji.

Teraz możesz [przypisać nazwy użytkowników, które zawierają niestandardową nazwę domeny](active-directory-users-create-azure-portal.md). Można tworzyć konta użytkowników w chmurze lub aktualizacji poprzednio zsynchronizowanych lokalnych informacji o koncie użytkownika, przy użyciu niestandardowej nazwy domeny. Możesz również zmienić synchronizowane konta użytkownika domeny sufiks informacji przy użyciu [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) lub [interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Można sumują się do maksymalnie 900 nazw domeny zarządzanej. Jeśli konfigurujesz wszystkie domeny dla Federacji lokalnego z usługą Active Directory, możesz dodać maksymalnie do 450 nazwy domeny w każdym katalogu. Aby uzyskać więcej informacji, zobacz [federacyjnym i nazwy domeny zarządzanej](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli nie można zweryfikować niestandardowej nazwy domeny, spróbuj wykonać następujące kroki:

1. **Zaczekaj godzinę**. Rekordy DNS musi propagację, aby zweryfikować domenę usługi Azure AD. Ten proces może potrwać godzinę lub dłużej.
2. **Upewnij się, że wprowadzono rekord DNS i że jest on poprawny**. Ten krok należy wykonać w witrynie sieci Web u rejestratora nazw domen dla domeny. Usługi Azure AD nie można zweryfikować nazwy domeny, jeśli 
  * Wpis DNS nie jest obecny w pliku strefy DNS
  * Nie jest on identyczny z wpisem DNS dostarczonym przez usługę Azure AD. 
  
  Jeśli nie masz dostępu do aktualizowania rekordów DNS domeny w rejestrze nazw domen, udostępnij wpis DNS osobie lub zespołowi w organizacji, którzy mają taki dostęp, i poproś o dodanie wpisu DNS.
3. **Usuń nazwę domeny z innego katalogu w usłudze Azure AD**. Nazwę domeny można weryfikować tylko w jednym katalogu. Jeśli nazwa domeny jest obecnie zweryfikowana w innym katalogu, nie można zweryfikować w katalogu nowe do momentu usunięcia ich na jeden z nich. Aby dowiedzieć się więcej na temat usuwania nazw domen, przeczytaj artykuł [Zarządzanie niestandardowymi nazwami domen](active-directory-domains-manage-azure-portal.md).    

Powtórz kroki opisane w tym artykule, aby dodać poszczególnych nazwa domeny.

## <a name="learn-more"></a>Dowiedz się więcej
[Poglądowe omówienie niestandardowych nazw domen w usłudze Azure AD](active-directory-domains-manage-azure-portal.md)

[Zarządzanie niestandardowymi nazwami domen](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Kolejne kroki
W tym szybkiego startu kiedy znasz już jak dodać niestandardową domenę do usługi Azure AD. 

Poniższe łącze służy do dodawania nowej domeny niestandardowej w usłudze Azure AD z portalu Azure.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 