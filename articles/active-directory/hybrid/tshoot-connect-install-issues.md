---
title: Rozwiązywanie problemów z instalacją usługi Azure AD Connect | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono kroki dotyczące rozwiązywania problemów z instalowaniem usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211841"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Rozwiązywanie problemów: Problemy z instalacją usługi Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane kroki**
Sprawdź, który [typ instalacji usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) jest odpowiedni dla Ciebie. Jeśli spełniasz kryteria instalacji ekspresowej, zalecamy, aby przejść z instalacji ekspresowej. Instalacja ekspresowa zapewnia minimalne opcje potrzebne do zakończenia instalacji, w związku z czym istnieje mniejsze prawdopodobieństwo wystąpienia jakichkolwiek problemów. 

Jeśli jednak nie spełniasz kryteriów instalacji ekspresowej i musisz wykonać instalację niestandardową, oto kilka najlepszych rozwiązań, które można wykonać, aby uniknąć typowych problemów. W trosce o prostocie wymienia się tu tylko opcje selektywne:

* Upewnij się, że jesteś administratorem na komputerze, na którym instalujesz AAD Connect. Zaloguj się do komputera przy użyciu tych samych poświadczeń administratora.

* Niech wszystkie opcje mają być domyślne na następnej stronie, z wyjątkiem "Użyj istniejącego programu SQL Server", jeśli chcesz użyć istniejącego programu SQL Server. Poniżej [przedstawiono więcej szczegółów](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) dotyczących korzystania z niestandardowych opcji instalacji. 

    ![Korzystanie z istniejącego programu SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na następnej stronie wybierz opcję "Utwórz nowe konto usługi AD", aby uniknąć problemów z uprawnieniami do istniejącego konta.

    ![Konto lasu usługi AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Typowe problemy**

* [Problemy z łącznością z lokalną usługą Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemy z łącznością z usługą Azure Active Directory w trybie online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemy z uprawnieniami z lokalną usługą Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Wymagania wstępne dotyczące programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Wybieranie typu instalacji do użycia dla programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: uaktualnianie z wcześniejszej wersji do najnowszej](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Usługa Azure AD Connect: Co to jest serwer przejściowy?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co to jest moduł ADConnectivityTool programu PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Następne kroki
- [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md)





