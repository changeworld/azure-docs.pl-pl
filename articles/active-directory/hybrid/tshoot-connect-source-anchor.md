---
title: 'Program Azure AD Connect: Rozwiązywanie problemów zakotwiczenia źródła podczas instalacji | Dokumentacja firmy Microsoft'
description: Ten temat zawiera procedurę rozwiązywania problemów z zakotwiczenie źródła podczas instalacji.
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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114159"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Rozwiązywanie problemów zakotwiczenia źródła, podczas instalacji
W tym artykule opisano różnych problemów, które mogą wystąpić podczas instalacji i ofert sposoby rozwiązania tych problemów związanych z zakotwiczenie źródła.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Nieprawidłowe źródło zakotwiczenia w usłudze Azure Active Directory

### <a name="custom-installation"></a>Instalacja niestandardowa

Podczas instalacji niestandardowej usługi Azure AD Connect odczytuje zasad zakotwiczenia źródła z usługą Azure Active Directory. Jeśli zasady istnieją w usłudze Azure Active Directory, Azure AD Connect stosuje te same zasady, chyba że zostanie on przesłonięty przez klienta. Kreator informuje użytkownika, w który atrybut został odczytany. Ponadto kreator wyświetli ostrzeżenie, jeśli podczas próby zastąpienia zasad zakotwiczenia źródła.

Podczas tej operacji odczytu jest możliwe, że zasady zakotwiczenie źródła w usłudze Azure Active Directory jest nieoczekiwany. W takim przypadku program Azure AD Connect nie może określić, jakie zakotwiczenie źródła do użycia i wymaga ręczne przesłonięcie.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Aby rozwiązać ten problem, można ręcznie przezwyciężyć, wybierając określony atrybut zakotwiczenia źródła. Korzystać z tej opcji tylko wtedy, gdy masz pewność, której atrybutu do wybrania. Jeśli nie masz niektórych, skontaktuj się z [pomocy technicznej firmy Microsoft](https://support.microsoft.com/contactus/) wskazówki. W przypadku zmiany zasad zakotwiczenie źródła może przerwać skojarzenie między lokalnych użytkowników i ich skojarzonych zasobów platformy Azure.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalacji ekspresowej program Azure AD Connect odczytuje zasad zakotwiczenia źródła z usługą Azure Active Directory. Jeśli zasady istnieją w usłudze Azure Active Directory, Azure AD Connect stosuje te same zasady. Nie ma opcji celu ręczne przesłonięcie.

Podczas tej operacji odczytu jest możliwe, że zasady zakotwiczenie źródła w usłudze Azure Active Directory jest nieoczekiwany. W takim przypadku program Azure AD Connect nie wie, co powinno być zakotwiczenie źródła.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Aby rozwiązać ten problem, należy ponownie zainstalować przy użyciu trybu niestandardowego i ręcznie przezwyciężyć zakotwiczenia źródła, wybierając określony atrybut. Korzystać z tej opcji tylko wtedy, gdy masz pewność, której atrybutu do wybrania. Jeśli nie masz niektórych, skontaktuj się z [pomocy technicznej firmy Microsoft](https://support.microsoft.com/contactus/) wskazówki. W przypadku zmiany zasad zakotwiczenie źródła może przerwać skojarzenie między lokalnych użytkowników i ich skojarzonych zasobów platformy Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Nieprawidłowe źródło zakotwiczenia w aparacie synchronizacji
Podczas instalacji, jest to możliwe, usługa Azure AD Connect próbuje Konfigurowanie aparatu synchronizacji przy użyciu elementu zakotwiczenia nieprawidłowe źródło. Ta operacja jest prawdopodobnie problem z produktem i instalacja programu Azure AD Connect zakończy się niepowodzeniem. Skontaktuj się z pomocą [pomocy technicznej firmy Microsoft](https://support.microsoft.com/contactus/) po uruchomieniu tego problemu.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).