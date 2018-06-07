---
title: Atrybuty cienia Usługa synchronizacji Azure AD Connect | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak działa atrybuty cienia w usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bd1ede2bf8ff642b7be0869e54a6f037b01dd262
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593415"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atrybuty cienia Usługa synchronizacji Azure AD Connect
Większość atrybutów są reprezentowane tak samo w usłudze Azure AD, są one w lokalnej usługi Active Directory. Niektóre atrybuty mają niektóre specjalnej obsługi, a wartość atrybutu w usłudze Azure AD może być inna niż Azure AD Connect synchronizuje.

## <a name="introducing-shadow-attributes"></a>Wprowadzenie do atrybutów w tle
Niektóre atrybuty mają dwa oświadczenia w usłudze Azure AD. Zarówno wartość lokalnie, jak i obliczonej wartości są przechowywane. Te atrybuty dodatkowe są nazywane atrybuty cienia. Są dwa atrybuty najbardziej typowych, w której występuje ten problem **userPrincipalName** i **proxyAddress**. Zmiany w wartościach atrybutów się stanie, gdy znajdują się wartości tych atrybutów reprezentujących domeny z systemem innym niż zweryfikowane. Ale aparatu synchronizacji w Connect odczytuje wartości w atrybucie tle tak z jego perspektywy, atrybut zostało potwierdzone przez usługę Azure AD.

Użytkownik nie widzi atrybuty cienia korzysta z platformy Azure, w portalu lub przy użyciu programu PowerShell. Ale zapoznanie pojęcia ułatwiają rozwiązywanie niektórych scenariuszy, w przypadku, gdy atrybut ma różne wartości lokalnej i w chmurze.

Aby lepiej zrozumieć zachowania, Szukaj w tym przykładzie z firmy Fabrikam:  
![Domeny](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Mają one wielu sufiksów nazw UPN w usłudze Active Directory ich lokalnych, ale tylko jeden zweryfikowane.

### <a name="userprincipalname"></a>userPrincipalName
Użytkownik ma następujące wartości atrybutów w domenie z systemem innym niż zweryfikowane:

| Atrybut | Wartość |
| --- | --- |
| userPrincipalName lokalnymi | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| UserPrincipalName usługi Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

Atrybut userPrincipalName jest wartość, która zostanie wyświetlona, gdy przy użyciu programu PowerShell.

Ponieważ wartość atrybutu rzeczywistego lokalnymi są przechowywane w usłudze Azure AD po zweryfikowaniu domeny fabrikam.com, usługi Azure AD zaktualizowanie atrybutu userPrincipalName przy użyciu wartości z shadowUserPrincipalName. Nie masz zsynchronizować zmiany z Azure AD Connect, które te wartości będą do zaktualizowania.

### <a name="proxyaddresses"></a>proxyAddresses
Ten sam proces, w celu uwzględnienia tylko zweryfikowanych domen występuje także dla proxyAddresses, ale niektóre dodatkowe logiki. Sprawdzanie zweryfikowanych domen odbywa się tylko dla skrzynek pocztowych użytkowników. Użytkownik z włączoną obsługą poczty lub skontaktuj się z reprezentują użytkownika w innej organizacji programu Exchange, a następnie można dodać wartości w polu proxyAddresses do tych obiektów.

W przypadku skrzynek pocztowych użytkownika lokalnie lub w usłudze Exchange Online są wyświetlane tylko wartości zweryfikowanych domen. Go może wyglądać następująco:

| Atrybut | Wartość |
| --- | --- |
| proxyAddresses lokalnymi | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| ProxyAddresses usługi Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

W takim przypadku **smtp:abbie.spencer@fabrikam.com** został usunięty, ponieważ nie został zweryfikowany tej domeny. Exchange również został dodany, ale **SIP:abbie.spencer@fabrikamonline.com**. Firma Fabrikam nie został użyty Lync/Skype lokalnej, ale usługi Azure AD i usługi Exchange Online przygotować.

Ta logikę proxyAddresses jest określany jako **ProxyCalc**. ProxyCalc została wywołana z każdej zmiany na koncie użytkownika po:

- Użytkownik został przypisany plan usługi, która obejmuje usługę Exchange Online, nawet jeśli użytkownik nie został licencji dla programu Exchange. Na przykład, jeśli użytkownik jest przypisany SKU E3 pakietu Office, ale tylko przypisano SharePoint Online. Dotyczy to nawet jeśli skrzynki pocztowej jest nadal lokalnymi.
- MsExchRecipientTypeDetails atrybut ma wartość.
- Możesz wprowadzić zmianę do proxyAddresses lub userPrincipalName.

ProxyCalc może potrwać pewien czas przetwarzania zmian na koncie użytkownika i nie jest równocześnie z procesu eksportowania Azure AD Connect.

> [!NOTE]
> Logika ProxyCalc ma pewne dodatkowe zachowania dla zaawansowanych scenariuszy, które nie zostały opisane w tym temacie. W tym temacie podano umożliwiające zrozumienie zachowania i zarządzania dokumentami nie wszystkie wewnętrzny logiki.

### <a name="quarantined-attribute-values"></a>Atrybuty poddane kwarantannie
Atrybuty cienia są również używane w przypadku zduplikowanymi wartościami atrybutów. Aby uzyskać więcej informacji, zobacz [odporności zduplikowany atrybut](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Zobacz także
* [Synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
