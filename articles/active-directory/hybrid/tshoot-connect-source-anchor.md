---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z kotwicą źródła podczas instalacji | Dokumenty firmy Microsoft'
description: W tym temacie przedstawiono kroki dotyczące rozwiązywania problemów z kotwicą źródłowej podczas instalacji.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114159"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Rozwiązywanie problemów z kotwicą źródła podczas instalacji
W tym artykule wyjaśniono różne problemy związane z zakotwiczeniem źródła, które mogą wystąpić podczas instalacji i oferuje sposoby rozwiązania tych problemów.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Nieprawidłowa kotwica źródła w usłudze Azure Active Directory

### <a name="custom-installation"></a>Instalacja niestandardowa

Podczas instalacji niestandardowej usługa Azure AD Connect odczytuje zasady zakotwiczenia źródła z usługi Azure Active Directory. Jeśli zasady istnieją w usłudze Azure Active Directory, usługa Azure AD Connect stosuje te same zasady, chyba że zostanie zastąpiona przez klienta. Kreator informuje, który atrybut został przeczytany. Ponadto kreator ostrzega, jeśli spróbujesz zastąpić zasady zakotwiczenia źródła.

Podczas tej operacji odczytu jest możliwe, że zasady zakotwiczenia źródła w usłudze Azure Active Directory jest nieoczekiwany. W takim przypadku usługa Azure AD Connect nie wie, co zakotwiczenie źródłowe do użycia i wymaga ręcznego zastąpienia.</br>
![Nieoczekiwane](media/tshoot-connect-source-anchor/source1.png)

Aby rozwiązać ten problem, można ręcznie zastąpić kotwicę źródłową, wybierając określony atrybut. Należy kontynuować tę opcję, jeśli i tylko wtedy, gdy masz pewność, który atrybut wybrać. Jeśli nie masz pewności, skontaktuj się z [pomocą techniczną firmy Microsoft,](https://support.microsoft.com/contactus/) aby uzyskać wskazówki. Jeśli zmienisz zasadę zakotwiczenia źródła, może to przerwać skojarzenie między użytkownikami lokalnymi i skojarzonymi z nimi zasobami platformy Azure.</br>
![Nieoczekiwane](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalacji ekspresowej usługa Azure AD Connect odczytuje zasady zakotwiczenia źródła z usługi Azure Active Directory. Jeśli zasady istnieją w usłudze Azure Active Directory, usługa Azure AD Connect stosuje te same zasady. Nie ma opcji ręcznego zastępowania.

Podczas tej operacji odczytu jest możliwe, że zasady zakotwiczenia źródła w usłudze Azure Active Directory jest nieoczekiwany. W takim przypadku usługa Azure AD Connect nie wie, co powinno być zakotwiczenie źródłowe.</br>
![Nieoczekiwane](media/tshoot-connect-source-anchor/source3.png)

Aby rozwiązać ten problem, należy ponownie zainstalować przy użyciu trybu niestandardowego i ręcznie zastąpić kotwicę źródłową, wybierając określony atrybut. Należy kontynuować tę opcję, jeśli i tylko wtedy, gdy masz pewność, który atrybut wybrać. Jeśli nie masz pewności, skontaktuj się z [pomocą techniczną firmy Microsoft,](https://support.microsoft.com/contactus/) aby uzyskać wskazówki. Jeśli zmienisz zasadę zakotwiczenia źródła, może to przerwać skojarzenie między użytkownikami lokalnymi i skojarzonymi z nimi zasobami platformy Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Nieprawidłowa kotwica źródłowa w silniku synchronizacji
Podczas instalacji możliwe jest, że usługa Azure AD Connect próbuje skonfigurować aparat synchronizacji przy użyciu nieprawidłowej kotwicy źródłowej. Ta operacja jest najprawdopodobniej problem z produktem i instalacja usługi Azure AD Connect zakończy się niepowodzeniem. Jeśli uruchomisz ten problem, skontaktuj się z pomocą techniczną firmy [Microsoft.](https://support.microsoft.com/contactus/)</br>
![Nieoczekiwane](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).