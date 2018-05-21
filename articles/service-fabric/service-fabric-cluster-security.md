---
title: Zabezpieczanie klastra sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat scenariusze zabezpieczeń klastra sieci szkieletowej usług Azure i różnych technologii, używanych do ich wdrażania.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: ac288575d29699d03728e74b73ed7359dd7252e2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenariusze zabezpieczeń klastra sieci szkieletowej usług
Klastra usługi sieć szkieletowa usług Azure jest zasobem, którego jesteś właścicielem. Jest obowiązek Zabezpieczanie klastrów w celu zapobiegania nieautoryzowanemu połączyć się z nimi. Bezpieczne klastra jest szczególnie ważne w przypadku obciążeń produkcyjnych są uruchomione w klastrze. Chociaż można utworzyć klastra niezabezpieczoną, jeśli klaster przedstawia punkty końcowe zarządzania do publicznej sieci internet, użytkowników anonimowych można się z nim połączyć. Niezabezpieczona klastrów nie są obsługiwane w przypadku obciążeń produkcyjnych. 

W tym artykule przedstawiono scenariusze zabezpieczeń platformy Azure i autonomicznych klastrów i różnych technologii, używanych do ich wdrażania:

* Zabezpieczenia węzła do węzła
* Węzeł klienta zabezpieczeń
* Kontrola dostępu oparta na rolach (RBAC)

## <a name="node-to-node-security"></a>Zabezpieczenia węzła do węzła
Węzła do węzła zabezpieczeń pomaga bezpiecznej komunikacji między maszynami wirtualnymi lub komputerów w klastrze. W tym scenariuszu zabezpieczeń gwarantuje, że tylko te komputery, które są autoryzowane do przyłączenia się do klastra mogą uczestniczyć w hosting aplikacji i usług w klastrze.

![Diagram komunikacji między węzłami][Node-to-Node]

Klastry z systemem Azure i autonomicznych klastrów z systemem Windows zarówno można użyć dowolnego [certyfikatu zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczenia systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx) dla komputerów z systemem Windows Server.

### <a name="node-to-node-certificate-security"></a>Zabezpieczenia certyfikatów węzła do węzła
Sieć szkieletowa usług korzysta z certyfikatów X.509, określone jako część konfiguracji typu węzła podczas tworzenia klastra. Na końcu tego artykułu spowoduje krótkie omówienie certyfikaty te są i jak uzyskać lub je utworzyć.

Konfigurowanie certyfikatu zabezpieczeń, podczas tworzenia klastra, w portalu Azure za pomocą szablonu usługi Azure Resource Manager lub za pomocą szablonu JSON autonomicznych. Można ustawić podstawowego certyfikatu i opcjonalne dodatkowej certyfikatu, który służy do najazdy certyfikatu. Podstawowe i pomocnicze certyfikaty ustawisz powinna się różnić od klienta administratora i certyfikatów klienta tylko do odczytu, które można ustawić dla [zabezpieczeń węzeł klient](#client-to-node-security).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń w klastrze platformy Azure, zobacz [Konfigurowanie klastra za pomocą szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń klastra dla klastra systemu Windows Server autonomicznych, zobacz [Secure autonomiczny klastra w systemie Windows za pomocą certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpieczenia systemu Windows węzła do węzła
Aby dowiedzieć się, jak skonfigurować zabezpieczenia systemu Windows dla klastra systemu Windows Server autonomicznych, zobacz [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Węzeł Klient zabezpieczeń służy do uwierzytelniania klientów i pomaga bezpiecznej komunikacji między klientem a poszczególne węzły w klastrze. Ten typ zabezpieczeń pomaga, upewnij się, czy tylko autoryzowani użytkownicy mają dostęp do klastra i aplikacje, które są wdrażane w klastrze. Klienci są identyfikowane za pomocą poświadczeń zabezpieczeń systemu Windows lub poświadczeń zabezpieczeń certyfikatów.

![Diagram komunikacji klienta do węzła][Client-to-Node]

Klastry z systemem Azure i autonomicznych klastrów z systemem Windows zarówno można użyć dowolnego [certyfikatu zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczenia systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Zabezpieczenia certyfikatów klienta do węzła
Ustaw zabezpieczenia certyfikat klienta do węzła podczas tworzenia klastra, w portalu Azure za pomocą szablonu usługi Resource Manager lub za pomocą szablonu JSON autonomicznych. Aby utworzyć certyfikat, Określ certyfikat klienta administratora lub certyfikatu klienta użytkownika. Najlepszym rozwiązaniem powinna się różnić od certyfikatów głównych i dodatkowych, określ dla klientów i użytkowników certyfikatów klienta administracyjnego można określić [zabezpieczeń węzła do węzła](#node-to-node-security). Domyślnie certyfikaty zabezpieczeń węzła do węzła klastra są dodawane do listy dozwolonych klienta administrator certyfikatów.

Klienci łączący się do klastra przy użyciu certyfikatu admin mają pełny dostęp do możliwości zarządzania. Klienci łączący się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do możliwości zarządzania. Te certyfikaty służą do RBAC, które jest opisane w dalszej części tego artykułu.

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń w klastrze platformy Azure, zobacz [Konfigurowanie klastra za pomocą szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń klastra dla klastra systemu Windows Server autonomicznych, zobacz [Secure autonomiczny klastra w systemie Windows za pomocą certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Węzeł Klient zabezpieczeń usługi Azure Active Directory na platformie Azure
Dla klastrów działających na platformie Azure można zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (Azure AD). Aby dowiedzieć się, jak utworzyć wymaganych artefaktów usługi Azure AD, jak można wypełniać ich podczas tworzenia klastra i jak połączyć się z klastrów później, zobacz [Konfigurowanie klastra za pomocą szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń
W przypadku klastrów platformy Azure dla węzła do węzła zabezpieczeń zalecane jest użycie zabezpieczeń usługi Azure AD na potrzeby uwierzytelniania klientów i certyfikatów.

Dla autonomicznych klastrów systemu Windows Server Jeśli masz system Windows Server 2012 R2 i Windows Active Directory, zaleca się użyć zabezpieczeń systemu Windows z kontami usług zarządzanymi przez grupę. W przeciwnym razie użyj zabezpieczenia systemu Windows przy użyciu kont systemu Windows.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Kontrola dostępu umożliwia ograniczanie dostępu do pewnych operacji klastra dla różnych grup użytkowników. Pozwala to zabezpieczyć klastra. Obsługiwane są dwa typy kontroli dostępu dla klientów nawiązujących połączenie z klastrem: rola administratora i roli użytkownika.

Użytkownicy, którzy są przypisani do roli administratora mają pełny dostęp do możliwości zarządzania, w tym do odczytu i zapisu możliwości. Użytkownicy, którzy mają przypisaną rolę użytkownika, domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwość wykonywania kwerend). Również rozwiązania aplikacji i usług.

Ustaw role administratora i użytkownika klienta podczas tworzenia klastra. Przypisywanie ról, zapewniając oddzielne tożsamości (na przykład przy użyciu certyfikatów lub usługi Azure AD) dla każdego typu roli. Aby uzyskać więcej informacji na temat domyślne ustawienia kontroli dostępu oraz sposobu zmiany ustawień domyślnych, zobacz [opartej na rolach kontroli dostępu dla klientów usługi sieć szkieletowa](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certyfikaty X.509 i sieci szkieletowej usług
Certyfikaty cyfrowe X.509 zwykle są używane do uwierzytelniania klientów i serwerów. Są używane do szyfrowania i cyfrowego podpisywania wiadomości. Aby uzyskać więcej informacji o certyfikatach cyfrowych X.509, zobacz [Praca z certyfikatami](http://msdn.microsoft.com/library/ms731899.aspx).

Kilka ważnych rzeczy, które należy uwzględnić:

* Aby utworzyć certyfikaty dla klastrów, w których są uruchomione obciążeń produkcyjnych, użyj poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub jednej z zatwierdzonych [certyfikatu urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority).
* Nigdy nie używaj żadnych tymczasowego lub Sprawdź certyfikaty, które utworzono za pomocą takich narzędzi jak MakeCert.exe w środowisku produkcyjnym.
* Można użyć certyfikatu z podpisem własnym, ale tylko w przypadku klastra testowego. Nie należy używać certyfikatu z podpisem własnym w środowisku produkcyjnym.

### <a name="server-x509-certificates"></a>Certyfikaty X.509 serwera
Certyfikaty serwera zostały zadania podstawowego uwierzytelniania serwera (węzeł) do klientów lub uwierzytelniania serwera (węzeł) na serwerze (węzeł). Gdy węzeł uwierzytelniania klienta lub węzeł jeden początkowej kontroli jest wartość nazwy pospolitej w **podmiotu** pola. Ta nazwa pospolita lub jeden z nazwy alternatywnej podmiotu certyfikatów (SAN) musi być obecny na liście dozwolonych nazw pospolitych.

Aby dowiedzieć się, jak wygenerować certyfikaty, które mają sieci SAN, zobacz [sposób dodawania alternatywna nazwa podmiotu do bezpiecznego certyfikatu LDAP](http://support.microsoft.com/kb/931351).

**Podmiotu** pole może mieć wielu wartości. Każda wartość jest prefiksem inicjowania, aby wskazać typ wartości. Zazwyczaj jest inicjowania **CN** (dla *nazwa pospolita*), na przykład **CN = www.contoso.com**. **Podmiotu** pole może być puste. Jeśli opcjonalny **alternatywna nazwa podmiotu** pole zostanie wypełnione, musi mieć zarówno nazwa pospolita certyfikatu i jednego wpisu na sieci SAN. Te są wprowadzane jako **nazwy DNS** wartości.

Wartość **zamierzone cele** pole certyfikatu powinna zawierać odpowiednie wartości, takich jak **uwierzytelniania serwera** lub **uwierzytelnianie klienta**.

### <a name="client-x509-certificates"></a>Certyfikaty X.509 klienta
Certyfikaty klienta nie są zwykle wydawane przez urząd certyfikacji innych firm. Zamiast tego magazynu osobistego bieżącej lokalizacji użytkownika zwykle zawiera certyfikaty klienta umieszczony przez urząd główny z **zamierzone cele** wartość **uwierzytelnianie klienta**. Klient może używać tego certyfikatu, gdy jest wymagane uwierzytelnianie wzajemne.

> [!NOTE]
> Wszystkie operacje zarządzania w klastrze usługi sieć szkieletowa usług wymagają certyfikatów serwera. Nie można używać certyfikatów klienta do zarządzania.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie klastra na platformie Azure przy użyciu szablonu usługi Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Tworzenie klastra przy użyciu portalu Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
