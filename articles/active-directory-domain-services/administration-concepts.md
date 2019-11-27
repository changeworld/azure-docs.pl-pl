---
title: Pojęcia dotyczące zarządzania dla Azure AD Domain Services | Microsoft Docs
description: Informacje na temat administrowania Azure Active Directory Domain Services domeną zarządzaną oraz zachowania kont użytkowników i haseł
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481022"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w programie Azure Active Directory Domain Services

Podczas tworzenia i uruchamiania domeny zarządzanej Azure Active Directory Domain Services (AD DS) istnieją pewne różnice w działaniu w porównaniu do tradycyjnego środowiska lokalnego AD DS. Używasz tych samych narzędzi administracyjnych na platformie Azure AD DS jako domeny samozarządzanej, ale nie możesz bezpośrednio uzyskać dostępu do kontrolerów domeny (DC). Istnieją także pewne różnice w zachowaniu zasad haseł i skrótów haseł w zależności od źródła tworzenia konta użytkownika.

W tym artykule opisano sposób administrowania domeną zarządzaną platformy Azure AD DS i różnego zachowania kont użytkowników w zależności od sposobu ich tworzenia.

## <a name="domain-management"></a>Zarządzanie domeną

Na platformie Azure AD DS kontrolery domeny, które zawierają wszystkie zasoby, takie jak użytkownicy i grupy, poświadczenia i zasady, są częścią usługi zarządzanej. W przypadku nadmiarowości dwa kontrolery domen są tworzone w ramach domeny zarządzanej AD DS platformy Azure. Nie można zalogować się do tych kontrolerów domeny w celu wykonywania zadań zarządzania. Zamiast tego należy utworzyć maszynę wirtualną zarządzania dołączoną do domeny zarządzanej AD DS platformy Azure, a następnie zainstalować regularne narzędzia do zarządzania AD DS. Możesz na przykład użyć przystawek Centrum administracyjne usługi Active Directory lub programu Microsoft Management Console (MMC), takich jak obiekty DNS lub zasady grupy.

## <a name="user-account-creation"></a>Tworzenie konta użytkownika

Konta użytkowników można tworzyć na platformie Azure AD DS na wiele sposobów. Większość kont użytkowników jest synchronizowana z usługą Azure AD, która może również obejmować konto użytkownika zsynchronizowane z lokalnego środowiska AD DS. Możesz również ręcznie utworzyć konta bezpośrednio w usłudze Azure AD DS. Niektóre funkcje, takie jak wstępnej synchronizacji haseł lub zasad haseł, zachowują się inaczej w zależności od tego, jak i gdzie są tworzone konta użytkowników.

* Konto użytkownika można synchronizować z poziomu usługi Azure AD. Dotyczy to również kont użytkowników tylko w chmurze utworzonych bezpośrednio w usłudze Azure AD i hybrydowych kont użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect.
    * Większość kont użytkowników w usłudze Azure AD DS jest tworzona za pośrednictwem procesu synchronizacji w usłudze Azure AD.
* Konto użytkownika można utworzyć ręcznie w domenie zarządzanej AD DS platformy Azure i nie istnieje w usłudze Azure AD.
    * Jeśli musisz utworzyć konta usług dla aplikacji uruchamianych tylko na platformie Azure AD DS, możesz je ręcznie utworzyć w domenie zarządzanej. Ponieważ synchronizacja jest jednokierunkowe z usługi Azure AD, konta użytkowników utworzone w usłudze Azure AD DS nie są synchronizowane z powrotem do usługi Azure AD.

## <a name="password-policy"></a>Zasady dotyczące haseł

Usługa Azure AD DS obejmuje domyślne zasady haseł, które definiują ustawienia dla elementów, takich jak blokada konta, maksymalny wiek hasła i złożoność hasła. Ustawienia, takie jak zasady blokady konta, mają zastosowanie do wszystkich użytkowników w usłudze Azure AD DS niezależnie od sposobu utworzenia użytkownika, jak opisano w poprzedniej sekcji. Niektóre ustawienia, takie jak minimalna długość hasła i złożoność hasła, mają zastosowanie tylko do użytkowników utworzonych bezpośrednio w usłudze Azure AD DS.

Możesz utworzyć własne niestandardowe zasady haseł, aby zastąpić zasady domyślne na platformie Azure AD DS. Te zasady niestandardowe można następnie zastosować do określonych grup użytkowników zgodnie z wymaganiami.

Aby uzyskać więcej informacji na temat różnic w sposobie stosowania zasad haseł w zależności od źródła tworzenia użytkowników, zobacz [zasady blokowania haseł i kont w domenach zarządzanych][password-policy].

## <a name="password-hashes"></a>Skróty haseł

Aby można było uwierzytelniać użytkowników w domenie zarządzanej, usługa Azure AD DS musi używać skrótów haseł w formacie, który jest odpowiedni dla protokołu NT LAN Manager (NTLM) i uwierzytelniania Kerberos. Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie zostanie włączona usługa Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie generować tych skrótów uwierzytelniania NTLM lub Kerberos w oparciu o istniejące poświadczenia użytkownika.

W przypadku kont użytkowników tylko w chmurze użytkownicy muszą zmienić swoje hasła, zanim będą mogli korzystać z usługi Azure AD DS. Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów haseł dla uwierzytelniania Kerberos i NTLM.

W przypadku użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect, należy [włączyć synchronizację skrótów haseł][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu AD DS platformy Azure dla dzierżawy usługi Azure AD. Starsze skróty haseł nie są używane, jeśli Azure AD Connect do synchronizowania środowiska lokalnego AD DS z usługą Azure AD.
>
> Jeśli starsze aplikacje nie korzystają z uwierzytelniania NTLM lub prostych powiązań LDAP, zalecamy wyłączenie synchronizacji skrótów haseł NTLM dla AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [wyłączanie słabych mechanizmów szyfrowania i synchronizacji skrótów poświadczeń NTLM][secure-domain].

Po odpowiednim skonfigurowaniu skróty do przydatnych haseł są przechowywane w domenie zarządzanej AD DS platformy Azure. Jeśli usuniesz domenę zarządzaną platformy Azure AD DS, wszystkie skróty haseł przechowywane w tym punkcie również zostaną usunięte. Nie można ponownie użyć synchronizowanych informacji o poświadczeniach w usłudze Azure AD, jeśli później utworzysz domenę zarządzaną platformy Azure AD DS Wcześniej przyłączone do domeny maszyny wirtualne lub użytkownicy nie będą mogli od razu przeprowadzić uwierzytelniania — usługa Azure AD musi generować i przechowywać skróty haseł w nowej domenie zarządzanej AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [proces synchronizacji skrótów haseł dla platformy Azure AD DS i Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Instalowanie Azure AD Connect w domenie zarządzanej AD DS platformy Azure nie jest obsługiwane do synchronizowania obiektów z powrotem do usługi Azure AD.

## <a name="forests-and-trusts"></a>Lasy i relacje zaufania

*Las* to logiczna konstrukcja używana przez Active Directory Domain Services (AD DS) do grupowania jednej lub wielu *domen*. Domeny następnie przechowują obiekty dla użytkowników lub grup i zapewniają usługi uwierzytelniania.

W usłudze Azure AD DS Las zawiera tylko jedną domenę. Lokalne lasy AD DS często zawierają wiele domen. W dużych organizacjach, szczególnie po fuzjach i przejęciach, może się okazać, że istnieje wiele lasów lokalnych, które każdy z nich będzie zawierać wiele domen.

Domyślnie domena zarządzana AD DS platformy Azure jest tworzona jako Las *użytkownika* . Ten typ lasu służy do synchronizowania wszystkich obiektów z usługi Azure AD, w tym wszystkich kont użytkowników utworzonych w środowisku lokalnym AD DS. Konta użytkowników mogą być uwierzytelniane bezpośrednio w domenie zarządzanej AD DS platformy Azure, na przykład w celu zalogowania się do maszyny wirtualnej przyłączonej do domeny. Las użytkownika działa, gdy można synchronizować skróty haseł, a użytkownicy nie korzystają z takich samych metod logowania, jak uwierzytelnianie karty inteligentnej.

W lesie *zasobów* AD DS platformy Azure użytkownicy uwierzytelniają się za pośrednictwem jednokierunkowego *zaufania* lasu z AD DS lokalnych. W tym podejściu skróty do obiektów użytkownika i haseł nie są synchronizowane z usługą Azure AD DS. Obiekty użytkownika i poświadczenia znajdują się tylko w AD DS lokalnej. Takie podejście umożliwia przedsiębiorstwom hostowanie zasobów i platform aplikacji na platformie Azure, które zależą od klasycznego uwierzytelniania, takich jak LDAPs, Kerberos lub NTLM, ale wszelkie problemy z uwierzytelnianiem lub wątpliwości są usuwane. Lasy zasobów usługi Azure AD DS są obecnie w wersji zapoznawczej.

Aby uzyskać więcej informacji na temat typów lasów w usłudze Azure AD DS, zobacz [co to są lasy zasobów?][concepts-forest] czy [relacje zaufania lasów działają w usłudze Azure AD DS?][concepts-trust]

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [Utwórz domenę zarządzaną platformy Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
