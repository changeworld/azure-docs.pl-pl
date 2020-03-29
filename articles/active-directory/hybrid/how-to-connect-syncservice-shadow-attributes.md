---
title: Atrybuty cienia usługi synchronizacji usługi Azure AD Connect | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak działają atrybuty cienia w usłudze synchronizacji usługi Azure AD Connect.
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
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60384723"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atrybuty w tle usługi synchronizacji usługi Azure AD Connect
Większość atrybutów są reprezentowane w taki sam sposób w usłudze Azure AD, ponieważ są one w lokalnej usłudze Active Directory. Ale niektóre atrybuty mają pewne specjalne obsługi i wartość atrybutu w usłudze Azure AD może być inna niż to, co synchronizuje usługi Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Przedstawiamy atrybuty cienia
Niektóre atrybuty mają dwie reprezentacje w usłudze Azure AD. Przechowywane są zarówno wartość lokalna, jak i obliczona. Te dodatkowe atrybuty są nazywane atrybutami cienia. Dwa najczęściej spotykane atrybuty, w których widzisz to zachowanie, to **userPrincipalName** i **proxyAddress**. Zmiana wartości atrybutów ma miejsce, gdy w tych atrybutach znajdują się wartości reprezentujące domeny niezweryfikowane. Ale aparat synchronizacji w Connect odczytuje wartość w atrybucie cienia, więc z jego perspektywy atrybut został potwierdzony przez usługę Azure AD.

Nie widać atrybutów cienia przy użyciu witryny Azure portal lub programu PowerShell. Jednak zrozumienie koncepcji pomaga rozwiązać niektóre scenariusze, w których atrybut ma różne wartości lokalnie i w chmurze.

Aby lepiej zrozumieć zachowanie, spójrz na ten przykład z firmy Fabrikam:  
![Domeny](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Mają wiele przyrostków sieci UPN w lokalnej usłudze Active Directory, ale zweryfikowali tylko jeden.

### <a name="userprincipalname"></a>userPrincipalName
Użytkownik ma następujące wartości atrybutów w domenie niezweryfikowane:

| Atrybut | Wartość |
| --- | --- |
| użytkownik lokalnyPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Nazwa użytkownika usługi Azure ADPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Atrybut userPrincipalName jest wartością, którą widzisz podczas korzystania z programu PowerShell.

Ponieważ rzeczywista wartość atrybutu lokalnego jest przechowywana w usłudze Azure AD, podczas weryfikacji domeny fabrikam.com usługa Azure AD aktualizuje atrybut userPrincipalName o wartości z shadowUserPrincipalName. Nie trzeba synchronizować żadnych zmian z usługi Azure AD Connect dla tych wartości, które mają zostać zaktualizowane.

### <a name="proxyaddresses"></a>proxyAddresses
Ten sam proces tylko w tym zweryfikowanych domen występuje również dla proxyAddresses, ale z pewną dodatkową logiką. Sprawdzanie zweryfikowanych domen odbywa się tylko dla użytkowników skrzynek pocztowych. Użytkownik lub kontakt z włączoną obsługą poczty reprezentuje użytkownika w innej organizacji programu Exchange i można dodać do tych obiektów dowolne wartości w proxy.

Dla użytkownika skrzynki pocztowej, lokalnie lub w usłudze Exchange Online, są wyświetlane tylko wartości dla zweryfikowanych domen. Może to wyglądać tak:

| Atrybut | Wartość |
| --- | --- |
| proxy lokalneAdresy | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Proxy exchange onlineAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

W tym przypadku **smtp:abbie.spencer\@fabrikam.com** został usunięty, ponieważ ta domena nie została zweryfikowana. Ale Exchange dodał również **SIP:abbie.spencer\@fabrikamonline.com**. Firma Fabrikam nie korzystała z programów Lync/Skype lokalnie, ale usługa Azure AD i usługa Exchange Online są do niej przygotowywane.

Ta logika dla proxyAddresses jest określany jako **ProxyCalc**. ProxyCalc jest wywoływany przy każdej zmianie użytkownika, gdy:

- Użytkownikowi przypisano plan usługi, który obejmuje usługę Exchange Online, nawet jeśli użytkownik nie otrzymał licencji na program Exchange. Jeśli na przykład użytkownikowi przypisano jednostkę SKU usługi Office E3, ale przypisano tylko program SharePoint Online. Jest to prawdą, nawet jeśli skrzynka pocztowa jest nadal lokalna.
- Atrybut msExchRecipientTypeDetails ma wartość.
- Należy wprowadzić zmianę proxyAddresses lub userPrincipalName.

ProxyCalc może zająć trochę czasu, aby przetworzyć zmiany na użytkownika i nie jest synchroniczne z procesem eksportowania usługi Azure AD Connect.

> [!NOTE]
> Logika ProxyCalc ma kilka dodatkowych zachowań dla zaawansowanych scenariuszy, które nie zostały udokumentowane w tym temacie. Ten temat jest dostępny, aby zrozumieć zachowanie, a nie udokumentować całą logikę wewnętrzną.

### <a name="quarantined-attribute-values"></a>Wartości atrybutów poddane kwarantannie
Atrybuty cienia są również używane, gdy istnieją zduplikowane wartości atrybutów. Aby uzyskać więcej informacji, zobacz [odporność atrybutów duplikatów](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Zobacz też
* [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
