---
title: Atrybuty w tle usługi Azure AD Connect sync | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak atrybuty w tle działają w usłudze Azure AD Connect sync service.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 07/13/2017
ms.date: 04/09/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384723"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atrybuty w tle usługi Azure AD Connect sync
Większość atrybutów są reprezentowane taki sam sposób w usłudze Azure AD, ponieważ znajdują się w usłudze Active Directory w środowisku lokalnym. Jednak niektóre atrybuty mają pewne specjalnej obsługi, a wartość atrybutu w usłudze Azure AD może być inny niż Azure AD Connect synchronizuje.

## <a name="introducing-shadow-attributes"></a>Wprowadzenie do atrybutów w tle
Niektóre atrybuty mają reprezentujących dwa w usłudze Azure AD. Wartość w środowisku lokalnym i obliczonej wartości są przechowywane. Te dodatkowe atrybuty są nazywane atrybutów w tle. Są dwa atrybuty najbardziej typowe tam, gdzie zobaczysz to zachowanie **userPrincipalName** i **proxyAddress**. Zmiany w wartościach atrybutów występuje, gdy istnieją wartości w tych atrybutów reprezentujących-zweryfikowane domeny. Ale aparatu synchronizacji w programie Connect odczytuje wartość atrybutu w tle czynności z jego punktu widzenia, atrybut zostało potwierdzone przez usługę Azure AD.

Nie widzisz atrybutów w tle, przy użyciu usługi Azure portal lub za pomocą programu PowerShell. Ale zrozumienie pojęcia pomaga rozwiązywać niektórych scenariuszy, w przypadku, gdy atrybut ma różne wartości w środowisku lokalnym i w chmurze.

Aby lepiej zrozumieć zachowanie, Przyjrzyj się w tym przykładzie z firmy Fabrikam:  
![Domeny](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Mają one wielu sufiksów nazw UPN w usłudze Active Directory ich w środowisku lokalnym, ale tylko jeden zweryfikowane.

### <a name="userprincipalname"></a>userPrincipalName
Użytkownik ma następujące wartości atrybutów w domenie zweryfikować:

| Atrybut | Wartość |
| --- | --- |
| userPrincipalName w środowisku lokalnym | lee.sperry@fabrikam.com |
| Usługa Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| UserPrincipalName usługi Azure AD | lee.sperry@fabrikam.partner.onmschina.cn |

Atrybut userPrincipalName jest wartością, która zostanie wyświetlony, gdy za pomocą programu PowerShell.

Ponieważ wartość atrybutu rzeczywistego lokalne są przechowywane w usłudze Azure AD, po zweryfikowaniu domeny fabrikam.com, usługi Azure AD aktualizuje atrybut userPrincipalName przy użyciu wartości z shadowUserPrincipalName. Nie masz zsynchronizować zmiany z usługi Azure AD Connect, które te wartości będą do zaktualizowania.

### <a name="proxyaddresses"></a>proxyAddresses
Ten sam proces, w tym tylko zweryfikowanych domen występuje także proxyAddresses, ale z niektórych dodatkowej logiki. Wyszukaj zweryfikowanych domen odbywa się tylko dla skrzynek pocztowych użytkowników. Użytkownik z włączoną obsługą poczty lub skontaktuj się z reprezentuje użytkownika w innej organizacji programu Exchange i można dodać żadnych wartości w polu proxyAddresses do tych obiektów.

W przypadku skrzynek pocztowych użytkownika lokalnie lub w usłudze Exchange Online są wyświetlane tylko wartości dla zweryfikowanych domen. Jego może wyglądać następująco:

| Atrybut | Wartość |
| --- | --- |
| w środowisku lokalnym proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| ProxyAddresses usługi Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

W tym przypadku **smtp:abbie.spencer\@fabrikam.com** została usunięta, ponieważ nie został zweryfikowany tej domeny. Dodano także programu Exchange, ale **SIP:abbie.spencer\@fabrikamonline.com**. Firma Fabrikam nie został użyty Lync/Skype w środowisku lokalnym, ale usługa Azure AD i usługi Exchange Online przygotować.

Tę logikę proxyAddresses jest określany jako **ProxyCalc**. ProxyCalc jest wywoływana z każdej zmiany na koncie użytkownika po:

- Użytkownik został przypisany planu usług, która obejmuje usługi Exchange Online, nawet wtedy, gdy użytkownik nie został licencji dla programu Exchange. Na przykład, jeśli użytkownik jest przypisany E3 jednostek SKU usługi Office, ale tylko przypisano SharePoint Online. Ta zasada obowiązuje, nawet jeśli Skrzynka pocztowa jest nadal w środowisku lokalnym.
- MsExchRecipientTypeDetails atrybut ma wartość.
- Wprowadzeniu zmiany proxyAddresses lub userPrincipalName.

ProxyCalc może zająć trochę czasu, aby przetworzyć zmiany na koncie użytkownika i nie jest synchroniczne z procesu eksportu program Azure AD Connect.

> [!NOTE]
> Logika ProxyCalc ma pewne dodatkowe zachowania w przypadku zaawansowanych scenariuszy nie udokumentowano w tym temacie. W tym temacie podano umożliwiające zrozumienie zachowania i zarządzania dokumentami nie wszystkie wewnętrzne logiki.

### <a name="quarantined-attribute-values"></a>Wartości atrybutów poddane kwarantannie
Atrybuty w tle są również używane w przypadku zduplikowanymi wartościami atrybutów. Aby uzyskać więcej informacji, zobacz [odporność na zduplikowane atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Zobacz także
* [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

<!-- Update_Description: wording update -->