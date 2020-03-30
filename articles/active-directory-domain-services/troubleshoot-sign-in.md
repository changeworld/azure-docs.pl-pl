---
title: Rozwiązywanie problemów z logowaniem w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe problemy i błędy związane z logiem użytkownika w Usługach domenowych Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612751"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z logowaniem do konta w domenie zarządzanej usług domenowych usługi Azure AD

Najczęstsze przyczyny konta użytkownika, który nie może zalogować się do domeny zarządzanej usługi Azure AD DS obejmują następujące scenariusze:

* [Konto nie jest jeszcze synchronizowane z usługą Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Usługi Azure AD DS nie ma skrótów haseł, aby umożliwić logowanie się do konta.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Konto jest zablokowane.](#the-account-is-locked-out)

> [!TIP]
> Usługi Azure AD DS nie można zsynchronizować w poświadczeniach dla kont, które są zewnętrzne dla dzierżawy usługi Azure AD. Użytkownicy zewnętrzni nie mogą zalogować się do domeny zarządzanej usług Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Konto nie jest jeszcze synchronizowane z usługą Azure AD DS

W zależności od rozmiaru katalogu może upłynąć trochę czasu, aby konta użytkowników i skróty poświadczeń były dostępne w usługach Azure AD DS. W przypadku dużych katalogów ta początkowa synchronizacja jednokierunkowa z usługi Azure AD może potrwać kilka godzin, a maksymalnie dzień lub dwa. Upewnij się, że czekasz wystarczająco długo przed ponowieniem próby uwierzytelnienia.

W środowiskach hybrydowych, w których użytkownik usługi Azure AD Connect synchronizuje dane katalogu lokalnego z usługą Azure AD, upewnij się, że uruchomiono najnowszą wersję usługi Azure AD Connect i [skonfigurowano usługę Azure AD Connect do pełnej synchronizacji po włączeniu usługi Azure AD DS.][azure-ad-connect-phs] Jeśli wyłączysz usługi Azure AD DS, a następnie ponownie włączysz, musisz wykonać te kroki ponownie.

Jeśli nadal występują problemy z kontami nie synchronizującymi za pośrednictwem usługi Azure AD Connect, uruchom ponownie usługę Azure AD Sync Service. Na komputerze z zainstalowaną usługą Azure AD Connect otwórz okno wiersza polecenia i uruchom następujące polecenia:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Usługi Azure AD DS nie mają skrótów haseł

Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie włączysz usług Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD również nie przechowuje żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie wygenerować tych skrótów haseł NTLM lub Kerberos na podstawie istniejących poświadczeń użytkowników.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Środowiska hybrydowe z synchronizacją lokalną

W środowiskach hybrydowych korzystających z usługi Azure AD Connect do synchronizacji z lokalnego środowiska usług AD DS można lokalnie generować i synchronizować wymagane skróty haseł NTLM lub Kerberos w usłudze Azure AD. Po utworzeniu domeny zarządzanej usługi Azure AD DS [włącz synchronizację skrótów haseł w Usługach domenowych Usługi domenowe Active Directory platformy Azure][azure-ad-connect-phs]. Bez ukończenia tego kroku synchronizacji skrótu hasła nie można zalogować się do konta przy użyciu usług Azure AD DS. Jeśli wyłączysz usługi Azure AD DS, a następnie ponownie włączysz, musisz wykonać te kroki ponownie.

Aby uzyskać więcej informacji, zobacz [Jak działa synchronizacja skrótów haseł dla usług Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Środowiska tylko w chmurze bez synchronizacji lokalnej

Domeny zarządzane usługi Azure AD DS bez synchronizacji lokalnej, tylko konta w usłudze Azure AD, również muszą generować wymagane skróty haseł NTLM lub Kerberos. Jeśli konto tylko w chmurze nie może się zalogować, ma pomyślnie zakończony proces zmiany hasła dla konta po włączeniu usługi Azure AD DS?

* **Nie, hasło nie zostało zmienione.**
    * [Zmień hasło do konta,][enable-user-accounts] aby wygenerować wymagane skróty haseł, a następnie odczekaj 15 minut, zanim spróbujesz zalogować się ponownie.
    * Jeśli wyłączysz usługi Azure AD DS, a następnie ponownie włączysz, każde konto musi wykonać kroki ponownie, aby zmienić swoje hasło i wygenerować wymagane skróty haseł.
* **Tak, hasło zostało zmienione.**
    * Spróbuj zalogować się przy użyciu formatu *UPN,* takiego jak `driley@aaddscontoso.com`, zamiast formatu *SAMAccountName,* takiego jak `AADDSCONTOSO\deeriley`.
    * *SAMAccountName* może być generowany automatycznie dla użytkowników, których prefiks nazwy UPN jest zbyt długi lub jest taki sam jak inny użytkownik w domenie zarządzanej. Format *upn* jest gwarantowane unikatowe w dzierżawie usługi Azure AD.

## <a name="the-account-is-locked-out"></a>Konto jest zablokowane

Konto użytkownika w usługach Azure AD DS jest zablokowane po spełnieniu zdefiniowanego progu dla nieudanych prób logowania. To zachowanie blokady konta ma na celu ochronę przed powtarzającymi się próbami logowania, które mogą wskazywać na automatyczny atak cyfrowy.

Domyślnie, jeśli w ciągu 2 minut zostanie podjętych 5 prób nieprawidłowego hasła, konto zostanie zablokowane na 30 minut.

Aby uzyskać więcej informacji i jak rozwiązać problemy z blokadą konta, zobacz [Rozwiązywanie problemów z blokadą konta w usługach Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy z dołączeniem maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS, [znajdź pomoc i otwórz bilet pomocy technicznej dla usługi Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
