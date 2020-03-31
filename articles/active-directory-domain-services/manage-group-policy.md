---
title: Tworzenie zasad grupy i zarządzanie nimi w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak edytować wbudowane obiekty zasad grupy (GPO) i tworzyć własne zasady niestandardowe w domenie zarządzanej usług domenowych Usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: bce71355eef19ec3cc85525033274f57b1a3e0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946421"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Administrowanie zasadami grupy w domenie zarządzanej usług domenowych usługi azure AD

Ustawieniami obiektów użytkowników i komputerów w usługach domenowych Active Directory platformy Azure (Usługi Usługi Azure AD DS) są często zarządzane przy użyciu obiektów zasad grupy(GPO). Usługi Azure AD DS zawiera wbudowane obiekty zasad grupy dla *użytkowników usługi AADDC* i *kontenery komputerów AADDC.* Te wbudowane obiekty zasad grupy można dostosować, aby skonfigurować zasady grupy zgodnie z potrzebami dla środowiska. Członkowie grupy *administratorów kontrolera domeny usługi Azure AD* mają uprawnienia do administrowania zasadami grupy w domenie usług Azure AD DS, a także mogą tworzyć niestandardowe obiekty zasad grupy i jednostki organizacyjne(OU). Więcej informacji na temat zasad grupy i ich działania można znaleźć [w omówienie zasad grupy][group-policy-overview].

W środowisku hybrydowym zasady grupy skonfigurowane w lokalnym środowisku usług AD DS nie są synchronizowane z usługą Azure AD DS. Aby zdefiniować ustawienia konfiguracji dla użytkowników lub komputerów w usługach Azure AD DS, edytuj jeden z domyślnych obiektów zasad grupy lub utwórz niestandardowy obiekt zasad grupy.

W tym artykule pokazano, jak zainstalować narzędzia zarządzania zasadami grupy, a następnie edytować wbudowane obiekty zasad grupy i utworzyć niestandardowe obiekty zasad grupy.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

> [!NOTE]
> Szablonów administracyjnych zasad grupy można używać, kopiując nowe szablony na stację roboczą zarządzania. Skopiuj pliki `%SYSTEMROOT%\PolicyDefinitions` *admx* i skopiuj `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`pliki `Language-CountryRegion` *adml* specyficzne dla ustawień regionalnych do programu , gdzie jest zgodne z językiem i regionem plików *adml.*
>
> Na przykład skopiuj do `\en-us` folderu angielską, amerykańską wersję plików *adml.*
>
> Alternatywnie można centralnie przechowywać szablon administracyjny zasad grupy na kontrolerach domeny, które są częścią domeny zarządzanej usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Jak utworzyć szablony administracyjne zasad grupy w magazynie centralnym dla szablonów administracyjnych zasad grupy w systemie Windows i zarządzać nimi.](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)

## <a name="install-group-policy-management-tools"></a>Instalowanie narzędzi do zarządzania zasadami grupy

Aby utworzyć i skonfigurować obiekt zasad grupy (GPO), należy zainstalować narzędzia zarządzania zasadami grupy. Narzędzia te można zainstalować jako funkcję w systemie Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz [instalowanie narzędzi administracji zdalnej (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące łączenia się przy użyciu portalu Azure portal, zobacz [Łączenie się z maszyną wirtualną systemu Windows Server][connect-windows-server-vm].
1. **Menedżer serwera** powinien być domyślnie otwarty po zalogowaniu się do maszyny Wirtualnej. Jeśli nie, w menu **Start** wybierz polecenie **Menedżer serwera**.
1. W okienku *Pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję Dodaj role **i funkcje**.
1. Na stronie **Przed rozpoczęciem** *Kreatora dodawania ról i funkcji*wybierz pozycję **Dalej**.
1. W przypadku *typu instalacji*pozostaw zaznaczoną opcję **instalacji opartą na rolach lub funkcjach** i wybierz pozycję **Dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, taką jak *myvm.aaddscontoso.com*, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Role serwera** kliknij przycisk **Dalej**.
1. Na stronie **Funkcje** wybierz funkcję **Zarządzanie zasadami grupy.**

    ![Instalowanie "Zarządzania zasadami grupy" na stronie Funkcje](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na stronie **Potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi zarządzania zasadami grupy może potrwać minutę lub dwie.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij,** aby zakończyć pracę **kreatora Dodawanie ról i funkcji.**

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Otwieranie Konsoli zarządzania zasadami grupy i edytowanie obiektu

Domyślne obiekty zasad grupy (GPO) istnieją dla użytkowników i komputerów w domenie zarządzanej usług Azure AD DS. Po zainstalowaniu funkcji Zarządzanie zasadami grupy z poprzedniej sekcji wyświetlmy i edytujmy istniejący obiekt zasad grupy. W następnej sekcji należy utworzyć niestandardowy obiekt zasad grupy.

> [!NOTE]
> Aby administrować zasadami grupy w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny usługi AAD.*

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **zarządzanie zasadami grupy** zainstalowanymi w poprzedniej sekcji.
1. Aby otworzyć konsolę zarządzania zasadami grupy (GPMC), wybierz pozycję **Zarządzanie zasadami grupy**.

    ![Konsola zarządzania zasadami grupy otwiera się gotowa do edytowania obiektów zasad grupy](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Istnieją dwa wbudowane obiekty zasad grupy (GPO) w domenie zarządzanej usługi Azure AD DS — jeden dla *kontenera Komputery AADDC* i jeden dla *kontenera użytkownicy usługi AADDC.* Obiekty zasad grupy można dostosować, aby skonfigurować zasady grupy zgodnie z potrzebami w domenie zarządzanej usług Azure AD DS.

1. W konsoli **Zarządzanie zasadami grupy** rozwiń węzeł **Forest: aaddscontoso.com.** Następnie rozwiń **węzły Domeny.**

    Istnieją dwa wbudowane kontenery dla *komputerów AADDC* i *użytkowników AADDC.* Każdy z tych kontenerów ma domyślny obiekt zasad grupy zastosowany do nich.

    ![Wbudowane obiekty zasad grupy zastosowano do domyślnych kontenerów "Komputery AADDC" i "Użytkownicy AADDC"](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Te wbudowane obiekty zasad grupy można dostosować do konfigurowania określonych zasad grupy w domenie zarządzanej usług Ad DS platformy Azure. Wybierz prawym przyciskiem wyboru jeden z zasad grupy, takich jak *obiekt zasad grupy komputerów AADDC,* a następnie wybierz polecenie **Edytuj...**.

    ![Wybierz opcję "Edytuj" jeden z wbudowanych zasad grupy](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Zostanie otwarte narzędzie Edytor zarządzania zasadami grupy, aby umożliwić dostosowanie obiektu zasad grupy, takie jak *Zasady konta:*

    ![Dostosowywanie obiektu zasad grupy, aby skonfigurować ustawienia zgodnie z wymaganiami](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Po zakończeniu wybierz **pozycję Plik > Zapisz,** aby zapisać zasady. Komputery domyślnie odświeżają zasady grupy co 90 minut i stosują wprowadzone zmiany.

## <a name="create-a-custom-group-policy-object"></a>Tworzenie niestandardowego obiektu zasad grupy

Aby zgrupować podobne ustawienia zasad, często należy utworzyć dodatkowe obiekty zasad grupy zamiast stosować wszystkie wymagane ustawienia w pojedynczym, domyślnym obiekcie zasad grupy. Za pomocą usługi Azure AD DS można tworzyć lub importować własne obiekty zasad grupy niestandardowej i łączyć je z niestandardową instalacją organizacyjną. Jeśli chcesz najpierw utworzyć niestandardową jednostkę organizacyjną, zobacz [tworzenie niestandardowej jednostki organizacyjnej w domenie zarządzanej usług Azure AD DS](create-ou.md).

1. W konsoli **Zarządzanie zasadami grupy** wybierz niestandardową jednostkę organizacyjną (OU), taką jak *MyCustomOU*. Wybierz prawym przyciskiem wyboru oku i wybierz pozycję **Utwórz obiekt zasad grupy w tej domenie, a następnie połącz go tutaj...**

    ![Tworzenie niestandardowego obiektu zasad grupy w konsoli Zarządzanie zasadami grupy](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Określ nazwę nowego obiektu zasad grupy, na przykład *Mój niestandardowy obiekt zasad grupy,* a następnie wybierz przycisk **OK**. Opcjonalnie można oprzeć ten niestandardowy obiekt zasad grupy na istniejącym obiekcie zasad i zestaw opcji zasad.

    ![Określanie nazwy nowego niestandardowego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Niestandardowy obiekt zasad grupy jest tworzony i połączony z niestandardową instalacją organizacyjną. Aby teraz skonfigurować ustawienia zasad, wybierz prawym przyciskiem rzeczy niestandardowy obiekt zasad grupy i wybierz polecenie **Edytuj...**:

    ![Wybierz opcję "Edytuj" niestandardowy obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Zostanie otwarty **Edytor zarządzania zasadami grupy,** aby umożliwić dostosowanie obiektu zasad grupy:

    ![Dostosowywanie obiektu zasad grupy, aby skonfigurować ustawienia zgodnie z wymaganiami](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Po zakończeniu wybierz **pozycję Plik > Zapisz,** aby zapisać zasady. Komputery domyślnie odświeżają zasady grupy co 90 minut i stosują wprowadzone zmiany.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dostępnych ustawień zasad grupy, które można skonfigurować za pomocą Konsoli zarządzania zasadami grupy, zobacz [Praca z elementami preferencji zasad grupy][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
