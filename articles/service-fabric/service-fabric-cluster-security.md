---
title: Zabezpieczanie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Poznaj scenariusze zabezpieczeń klastra usługi Azure Service Fabric oraz różnych technologii używanych do ich wykonania.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: 6d67fa4af031480fda4a91f7356bff69830a654c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667603"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenariusze zabezpieczeń klastra usługi Service Fabric
Klaster usługi Azure Service Fabric jest zasobem, którego jesteś właścicielem. Jest odpowiedzialny za Zabezpieczanie klastrów zapobiega nieautoryzowanym użytkownikom nawiązywanie połączeń z nich. Zabezpieczonego klastra jest szczególnie ważne w przypadku obciążeń produkcyjnych są uruchomione w klastrze. Chociaż można utworzyć z niezabezpieczonym klastrem, jeśli klaster udostępnia punktów końcowych zarządzania do publicznej sieci internet, użytkowników anonimowych można się z nim. Niezabezpieczonych klastrów nie są obsługiwane w przypadku obciążeń produkcyjnych. 

W tym artykule przedstawiono omówienie scenariuszy zabezpieczeń dla klastrów platformy Azure i autonomicznych klastrów i różnych technologii używanych do ich wykonania:

* Węzeł węzeł zabezpieczeń
* Węzeł klienta zabezpieczeń
* Kontrola dostępu oparta na rolach (RBAC)

## <a name="node-to-node-security"></a>Węzeł węzeł zabezpieczeń
Węzeł węzeł zabezpieczeń pomaga bezpiecznej komunikacji między maszynami wirtualnymi lub komputerów w klastrze. W tym scenariuszu zabezpieczeń gwarantuje, że tylko te komputery, które są autoryzowane do przyłączenia się do klastra można uczestniczyć w hostingu aplikacji i usług w klastrze.

![Diagram przedstawiający komunikacji między węzłami][Node-to-Node]

Klastry z systemem w klastrach platformy Azure i autonomicznych z systemem Windows zarówno można użyć dowolnego [certyfikatów zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń Windows](https://msdn.microsoft.com/library/ff649396.aspx) dla komputerów z systemem Windows Server.

### <a name="node-to-node-certificate-security"></a>Węzeł węzeł certyfikatów zabezpieczeń
Usługa Service Fabric używa certyfikatów serwera X.509, które określisz w ramach konfiguracji typu węzła, podczas tworzenia klastra. Na końcu tego artykułu widać krótkie omówienie te certyfikaty są i jak można uzyskać lub je utworzyć.

Konfigurowanie certyfikatów zabezpieczeń podczas tworzenia klastra w witrynie Azure portal przy użyciu szablonu usługi Azure Resource Manager lub przy użyciu szablonu JSON autonomicznych. Usługa Service Fabric SDK domyślne zachowanie to można wdrożyć i zainstalować certyfikat z najdalej w przyszłości Wygasający certyfikat; zachowanie klasycznej dozwolone Definiowanie głównego i dodatkowego certyfikatu, aby umożliwić najazdy inicjowana ręcznie i nie są zalecane do użytku przez nowe funkcje. Podstawowe certyfikaty, które będą znajdować się użyć ma najdalej w przyszłości wygasające, powinny różnić się od klienta administratora i certyfikaty klienta tylko do odczytu, które można ustawić dla [węzeł klienta zabezpieczeń](#client-to-node-security).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń w klastrze na platformie Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń klastra autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie klastra autonomicznego w Windows przy użyciu certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Węzeł węzeł Windows zabezpieczeń
Informacje na temat konfigurowania zabezpieczeń Windows autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie klastra autonomicznego w Windows przy użyciu zabezpieczeń Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Węzeł klienta zabezpieczeń
Węzeł klienta zabezpieczeń uwierzytelnia klientów i pomaga bezpiecznej komunikacji między klientem a poszczególnych węzłów w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mogą dostęp do klastra i aplikacje, które są wdrażane w klastrze. Klienci są identyfikowane za pomocą ich poświadczeń zabezpieczeń Windows lub ich poświadczeń zabezpieczeń certyfikatu.

![Diagram przedstawiający komunikacji klient węzeł][Client-to-Node]

Klastry z systemem w klastrach platformy Azure i autonomicznych z systemem Windows zarówno można użyć dowolnego [certyfikatów zabezpieczeń](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Węzeł klienta certyfikatu zabezpieczeń
Konfigurowanie zabezpieczeń certyfikatu węzeł klienta, podczas tworzenia klastra w witrynie Azure portal przy użyciu szablonu usługi Resource Manager lub przy użyciu szablonu JSON autonomicznych. Aby utworzyć certyfikat, należy określić certyfikat klienta administracyjnego lub certyfikatu klienta użytkownika. Najlepszym rozwiązaniem jest administracyjnej klientów i użytkowników certyfikaty klienta można określić powinny różnić się od głównego i dodatkowego certyfikatu dla [zabezpieczeń między węzłami](#node-to-node-security). Domyślnie certyfikaty zabezpieczeń między węzłami klastra są dodawane do listy certyfikatów admin klienta dozwolone.

Klienci, którzy łączenia z klastrem przy użyciu certyfikatu administratora mają pełny dostęp do możliwości zarządzania. Klienci, którzy łączą się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do możliwości zarządzania. Te certyfikaty są używane dla RBAC, który jest opisany w dalszej części tego artykułu.

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń w klastrze na platformie Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować certyfikat zabezpieczeń klastra autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie klastra autonomicznego w Windows przy użyciu certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Węzeł klienta zabezpieczeń usługi Azure Active Directory na platformie Azure
Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Aplikacje są podzielone na tych z opartą na sieci web, interfejs użytkownika logowania i te w środowisku klienta natywnego. Jeśli nie utworzono już dzierżawę, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym, oparta na sieci web [narzędzia Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W rezultacie utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra, jednej aplikacji sieci web i jednej aplikacji natywnej.

W przypadku klastrów działających na platformie Azure można zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (Azure AD). Aby dowiedzieć się, jak utworzyć wymaganych artefaktów w usłudze Azure AD i wypełnić je podczas tworzenia klastra, zobacz [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń
W przypadku klastrów usługi Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dla wzajemnego uwierzytelniania klienta i węzła są następujące:
*   Używanie usługi Azure Active Directory na potrzeby określania tożsamości klienta
*   Certyfikat na potrzeby potwierdzania tożsamości serwera oraz szyfrowania SSL komunikacji HTTP

Dla klastrów usługi Service Fabric jest wdrażany w sieci publicznych, hostowanych na platformie Azure zalecenie dotyczące zabezpieczeń między węzłami jest użycie certyfikatu klastra do uwierzytelniania węzłów. 


W przypadku autonomicznych klastrów systemu Windows Server w przypadku systemu Windows Server 2012 R2 i Windows Active Directory, zaleca się używać zabezpieczeń Windows za pomocą kont usługi zarządzanych przez grupę. W przeciwnym razie za pomocą Windows zabezpieczenia konta Windows.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Można użyć kontroli dostępu, aby ograniczyć dostęp do pewnych operacji klastra dla różnych grup użytkowników. Dzięki temu zabezpieczeniu klastra. Dwa typy kontroli dostępu są obsługiwane w przypadku klientów nawiązujących połączenie z klastrem: Rola administratora i roli użytkownika.

Użytkownicy, którzy są przypisani do roli Administrator ma pełny dostęp do funkcji zarządzania, w tym do odczytu i zapisu funkcje. Użytkownicy z przypisaną rolą użytkownika, domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytań). Mogą również można rozwiązać, aplikacji i usług.

Ustaw role administratora i użytkownika klienta podczas tworzenia klastra. Przypisz role, zapewniając osobne tożsamości (np. przy użyciu certyfikatów lub Azure AD) dla poszczególnych ról. Aby uzyskać więcej informacji na temat domyślne ustawienia kontroli dostępu oraz sposobu zmiany ustawień domyślnych, zobacz [opartej na rolach kontrola dostępu dla klientów usługi Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certyfikaty X.509 i Service Fabric
Cyfrowe certyfikaty X.509 często są używane do uwierzytelniania klientów i serwerów. Są używane do szyfrowania i cyfrowego podpisywania wiadomości. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i udostępnia funkcje zabezpieczeń aplikacji. Aby uzyskać więcej informacji na temat certyfikatów cyfrowych X.509, zobacz [Praca z certyfikatami](https://msdn.microsoft.com/library/ms731899.aspx). Możesz użyć [usługi Key Vault](../key-vault/key-vault-overview.md) do zarządzania certyfikatami klastrów usługi Service Fabric na platformie Azure.

Niektóre ważne kwestie należy wziąć pod uwagę:

* Aby utworzyć certyfikaty dla klastrów, które są uruchomione obciążeń produkcyjnych, użyj poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub jednej z zatwierdzonych [certyfikatu urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority).
* Nie wolno używać żadnych tymczasowych produkcją lub Testuj certyfikaty, które tworzysz przy użyciu narzędzi, takich jak MakeCert.exe w środowisku produkcyjnym.
* Można użyć certyfikatu z podpisem własnym, ale tylko w przypadku klastra testowego. Nie należy używać certyfikatu z podpisem własnym w środowisku produkcyjnym.
* Podczas generowania odcisk palca certyfikatu, należy wygenerować odcisk SHA1. Algorytm SHA1 jest, co to jest używana podczas konfigurowania klienta i klaster odciski palców certyfikatu.

### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagane)
Te certyfikaty (jedną podstawową i opcjonalnie pomocniczego) są wymagane do zabezpieczenia klastra i zapobiegania nieuprawnionemu dostępowi do niego. Te certyfikaty, udostępniają klastra i serwera uwierzytelniania.

Uwierzytelnianie klastra uwierzytelnia komunikacji między węzłami dla Federacji klastra. Tylko węzły, które można potwierdzić swoją tożsamość za pomocą tego certyfikatu można dołączyć do klastra. Uwierzytelnianie serwera uwierzytelnia punktów końcowych zarządzania klastrem na kliencie zarządzania tak, aby klient zarządzania wie, że rozmawia rzeczywistych klastra, a nie "człowiek pośrodku". Ten certyfikat zapewnia również protokół SSL dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer, za pośrednictwem protokołu HTTPS. Kiedy klient lub węzeł uwierzytelnia węzła, jest jeden wstępne kontrole wartości nazwy pospolitej w **podmiotu** pola. Ta nazwa pospolita lub jednego z nazwy alternatywnej podmiotu certyfikaty (SAN) musi być obecny na liście dozwolonych nazw pospolitych.

Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny. Te certyfikaty, zwykle mają rozszerzenia pfx lub PEM  
* Certyfikat musi zostać utworzona na potrzeby wymiany kluczy, co umożliwia eksport do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi odpowiadać domeny, która umożliwia dostęp do klastra usługi Service Fabric**. To dopasowanie jest wymagane, aby zapewnić protokół SSL dla punktu końcowego zarządzania HTTPS klastra i narzędzia Service Fabric Explorer. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji (CA) dla *. cloudapp.azure.com domeny. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

Inne kwestie do rozważenia:

* **Podmiotu** pole może mieć wiele wartości. Każda wartość jest poprzedzony znakiem inicjowania, aby wskazać typ wartości. Zwykle jest inicjowania **CN** (dla *nazwa pospolita*), na przykład **CN = www\.contoso.com**. 
* **Podmiotu** pole może być pusta. 
* Jeśli opcjonalny **alternatywna nazwa podmiotu** wypełnianie pola, musi mieć zarówno nazwa pospolita certyfikatu i jednego wpisu na sieci SAN. Są one wprowadzane jako **nazwy DNS** wartości. Aby dowiedzieć się, jak generować certyfikaty, które mają sieci SAN, zobacz [sposób dodawania alternatywna nazwa podmiotu do bezpiecznego certyfikatu LDAP](https://support.microsoft.com/kb/931351).
* Wartość **przeznaczone do celów** pole certyfikatu powinien zawierać odpowiednią wartość, takich jak **uwierzytelniania serwera** lub **uwierzytelnianie klienta**.

### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów można zainstalować w klastrze ze względów bezpieczeństwa aplikacji. Przed utworzeniem klastra, należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają certyfikatów do zainstalowania w węzłach, takich jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.
* Szyfrowanie danych między węzłami podczas replikacji.

Pojęcie tworzenia zabezpieczonych klastrów jest taki sam, czy są one Linux lub Windows klastrów.

### <a name="client-authentication-certificates-optional"></a>Certyfikaty uwierzytelniania klienta (opcjonalnie)
Administrator lub użytkownik operacji klienta programu można określić dowolną liczbę dodatkowych certyfikatów. Klient może używać tego certyfikatu, gdy jest wymagane uwierzytelnianie wzajemne. Certyfikaty klienta nie są zwykle wydawane przez urząd certyfikacji innych firm. Zamiast tego w bieżącej lokalizacji użytkownika w magazynie osobistym zazwyczaj zawiera certyfikaty klienta, umieszczone tam przez główny urząd. Certyfikat powinien mieć **przeznaczone do celów** wartość **uwierzytelnianie klienta**.  

Domyślnie certyfikat klastra uprawnieniami administratora klienta. Tych dodatkowych certyfikatów klienta nie powinien być zainstalowany w klastrze, ale są określane jako są dozwolone w konfiguracji klastra.  Jednak certyfikaty klienta muszą być zainstalowane na maszynach klienckich do łączenia z klastrem i wykonywać dowolne operacje.

> [!NOTE]
> Wszystkie operacje zarządzania na klastrze usługi Service Fabric wymagają certyfikatów serwera. Nie można używać certyfikatów klienta do zarządzania.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie klastra na platformie Azure przy użyciu szablonu usługi Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Tworzenie klastra przy użyciu witryny Azure portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
