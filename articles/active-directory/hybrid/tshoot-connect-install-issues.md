---
title: Rozwiązywanie problemów z instalacją program Azure AD Connect | Dokumentacja firmy Microsoft
description: Ten temat zawiera procedurę rozwiązywania problemów z instalacją program Azure AD Connect.
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
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454795"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Rozwiązywanie problemów: Problemy z instalacją programu Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane czynności**
Należy sprawdzić, która [typu instalacji program Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) jest odpowiedni dla Ciebie. Jeśli spełniasz kryteria instalacji ekspresowej, następnie zdecydowanie zalecamy zawsze pod ręką instalacji ekspresowej. Instalacja ekspresowa oferuje opcje minimalnych wymaganych do ukończenia instalacji, w związku z tym jest mniej prawdopodobieństwo wystąpienia problemów. 

Jednak jeśli nie są spełnione kryteria instalacji ekspresowej, a należy wykonać instalację niestandardową, poniżej przedstawiono kilka najlepszych zasad, a następnie można wykonać w celu uniknięcia typowych problemów. Dla uproszczenia należy tylko selektywne opcje są wymienione w tym miejscu:

* Upewnij się, że jesteś administratorem na komputerze, na którym jest instalowany program AAD Connect. Zaloguj się maszynie za pomocą tych samych poświadczeń administratora.

* Poinformuj wszystkich opcji jako domyślna na następującej stronie, z wyjątkiem "Użyj istniejącego serwera SQL", jeśli chcesz użyć istniejącego serwera SQL Server. Poniżej przedstawiono [szczegółowe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) o tym, jak korzystać z opcji instalacji niestandardowej. 

    ![Użyj istniejącego serwera SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na następnej stronie wybierz opcję "Utwórz nowe AD konto", aby uniknąć wszelkich uprawnień problemy przy użyciu istniejącego konta.

    ![Usługi AD lasu konta](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Typowe problemy**

* [Problemy z połączeniem z usługą Active Directory w środowisku lokalnym](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemy z połączeniem z usługą online usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemy z uprawnieniami w usłudze Active Directory w środowisku lokalnym](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Prerequisites for Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites) (Wymagania wstępne programu Azure AD Connect)
* [Select which installation type to use for Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) (Wybieranie typu instalacji do użycia dla programu Azure AD Connect)
* [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Niestandardowa instalacja programu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Co to jest serwer przejściowy?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co to jest moduł ADConnectivityTool programu PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Kolejne kroki
- [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa? ](whatis-hybrid-identity.md).





