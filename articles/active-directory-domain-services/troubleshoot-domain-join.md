---
title: Rozwiązywanie problemów z dołączania do domeny za pomocą usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe problemy podczas próby dołączenia do domeny maszyny Wirtualnej lub połączenia aplikacji z Usługami domenowymi Usługi active directory platformy Azure i nie można połączyć się ani uwierzytelnić do domeny zarządzanej.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299112"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Rozwiązywanie problemów z przyłączaniem do domeny w domenie usługi Azure AD Domain Services

Podczas próby dołączenia do maszyny wirtualnej (VM) lub połączyć aplikację z domeną zarządzaną usług domenowych Active Directory platformy Azure (AD DS), może pojawić się błąd, którego nie możesz wykonać. Aby rozwiązać problemy z przyłączaniem do domeny, sprawdź, w którym z następujących punktów występuje problem:

* Jeśli nie zostanie wyświetlony monit o uwierzytelnienie, maszyna wirtualna lub aplikacja nie może połączyć się z domeną zarządzaną usługą Azure AD DS.
    * Zacznij rozwiązywać [problemy z łącznością w przypadku dołączania do domeny](#connectivity-issues-for-domain-join).
* Jeśli podczas uwierzytelniania zostanie wyświetlony błąd, połączenie z domeną zarządzaną usługą Azure AD DS zakończy się pomyślnie.
    * Zacznij rozwiązywać [problemy związane z poświadczeniami podczas dołączania do domeny](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemy z łącznością dla przyłącza do domeny

Jeśli maszyna wirtualna nie może znaleźć domeny zarządzanej usług Azure AD DS, zwykle występuje problem z połączeniem sieciowym lub konfiguracją. Zapoznaj się z następującymi krokami rozwiązywania problemów, aby zlokalizować i rozwiązać problem:

1. Upewnij się, że maszyna wirtualna jest połączona z tą samą lub równorzędną siecią wirtualną, która jest włączona dla usług Azure AD DS. Jeśli nie, maszyna wirtualna nie może znaleźć i połączyć się z domeną, aby dołączyć.
    * Jeśli maszyna wirtualna nie jest połączona z tą samą siecią wirtualną, upewnij się, że komunikacja równorzędna sieci wirtualnej lub połączenie sieci VPN jest *aktywne* lub *połączone,* aby umożliwić prawidłowy przepływ ruchu.
1. Spróbuj wykonać ping do domeny przy użyciu nazwy domeny domeny `ping aaddscontoso.com`zarządzanej usług Azure AD DS, takiej jak .
    * Jeśli odpowiedź ping nie powiedzie się, spróbuj wykonać ping do adresów IP domeny wyświetlanej na `ping 10.0.0.4`stronie przegląd w portalu domeny zarządzanej usług Azure AD DS, takiej jak .
    * Jeśli można pomyślnie pingować adres IP, ale nie domenę, system DNS może być niepoprawnie skonfigurowany. Upewnij się, że skonfigurowano serwery DNS domeny zarządzanej usług Azure AD DS dla sieci wirtualnej.
1. Spróbuj opróżnić pamięć podręczną programu rozpoznawania nazw `ipconfig /flushdns`DNS na maszynie wirtualnej, na przykład .

### <a name="network-security-group-nsg-configuration"></a>Konfiguracja sieciowej grupy zabezpieczeń (NSG)

Podczas tworzenia domeny zarządzanej usług Azure AD DS utworzona jest również grupa zabezpieczeń sieci z odpowiednimi regułami pomyślnej operacji domeny. Jeśli edytujesz lub tworzysz dodatkowe reguły sieciowej grupy zabezpieczeń, możesz przypadkowo blokować porty wymagane dla usług Azure AD DS w celu świadczenia usług połączenia i uwierzytelniania. Te reguły sieciowej grupy zabezpieczeń mogą powodować problemy, takie jak nieukończenie synchronizacji haseł, nie można się zalogować lub problemy z przyłączaniem do domeny.

Jeśli nadal występują problemy z połączeniem, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Sprawdź stan kondycji domeny zarządzanej usług Azure AD DS w witrynie Azure portal. Jeśli masz alert dla *usługi AADDS001,* reguła sieciowej grupy zabezpieczeń blokuje dostęp.
1. Przejrzyj [wymagane porty i reguły sieciowej grupy zabezpieczeń][network-ports]. Upewnij się, że do maszyny wirtualnej lub sieci wirtualnej, z której łączysz, nie są stosowane żadne reguły sieci zabezpieczeń, nie są one blokowe.
1. Po rozwiązaniu problemów z konfiguracją sieciowej grupy zabezpieczeń alert *AADDS001* zniknie ze strony kondycji w ciągu około 2 godzin. Dzięki łączności sieciowej teraz dostępne, spróbuj ponownie dołączyć do domeny maszyny wirtualnej.

## <a name="credentials-related-issues-during-domain-join"></a>Problemy związane z poświadczeniami podczas dołączania do domeny

Jeśli pojawi się okno dialogowe z prośbą o poświadczenia do przyłączenia się do domeny zarządzanej usług Azure AD DS, maszyna wirtualna może połączyć się z domeną przy użyciu sieci wirtualnej platformy Azure. Proces dołączania do domeny kończy się niepowodzeniem podczas uwierzytelniania w domenie lub autoryzacji w celu ukończenia procesu dołączania do domeny przy użyciu poświadczeń.

Aby rozwiązać problemy związane z poświadczeniami, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Spróbuj użyć formatu UPN, aby `dee@aaddscontoso.onmicrosoft.com`określić poświadczenia, takie jak . Upewnij się, że ta owana jest poprawnie ta owana liczba konfiguracji UPN w usłudze Azure AD.
    * *SamAccountName* dla twojego konta może być automatyczniegenerowany, jeśli w dzierżawie znajduje się wielu użytkowników z tym samym prefiksem nazwy UPN lub jeśli prefiks nazwy UPN jest zbyt długi. W związku z tym format *SAMAccountName* dla konta może się różnić od tego, czego oczekujesz lub używasz w domenie lokalnej.
1. Spróbuj użyć poświadczeń dla konta użytkownika, które jest częścią domeny zarządzanej usługi Azure AD DS, aby dołączyć maszyny wirtualne do domeny zarządzanej.
1. Upewnij się, że [masz włączoną synchronizację haseł][enable-password-sync] i czekałeś wystarczająco długo, aby początkowa synchronizacja hasła została zakończona.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać głębsze zrozumienie procesów usługi Active Directory w ramach operacji przyłączania do domeny, zobacz Problemy z [dołączaniem i uwierzytelnianiem][join-authentication-issues].

Jeśli nadal występują problemy z dołączeniem maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS, [znajdź pomoc i otwórz bilet pomocy technicznej dla usługi Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
