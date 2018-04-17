---
title: Tworzenie klastra sieci szkieletowej usług w portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skonfigurować bezpieczny klastra sieci szkieletowej usług na platformie Azure przy użyciu portalu Azure i usługi Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/09/2018
ms.author: aljo
ms.openlocfilehash: d3efc14886d82290241e9868ea9b0e8262efddea
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Tworzenie klastra sieci szkieletowej usług na platformie Azure przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Jest to przewodnik krok po kroku, który przeprowadzi Cię przez kolejne kroki konfigurowania klastra sieci szkieletowej usług (Linux lub Windows) na platformie Azure przy użyciu portalu Azure. Ten przewodnik przeprowadzi Cię przez następujące kroki:

* Utwórz klaster na platformie Azure za pośrednictwem portalu Azure.
* Uwierzytelnianie za pomocą certyfikatów Administratorzy.

> [!NOTE]
> Dla bardziej zaawansowanych opcji zabezpieczeń, takich jak uwierzytelnianie użytkowników w usłudze Azure Active Directory oraz konfigurowania certyfikatów do zabezpieczenia aplikacji [tworzenia klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpieczenia klastra 
W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Aby uzyskać więcej informacji dotyczących używania certyfikatów w sieci szkieletowej usług, zobacz [scenariusze zabezpieczeń klastra sieci szkieletowej usług][service-fabric-cluster-security].

Jeśli po raz pierwszy tworzysz klastra sieci szkieletowej usług lub wdrażania klastra dla testu obciążenia, możesz przejść do następnej sekcji (**Utwórz klaster w portalu Azure**) i system generował niezbędne certyfikaty klastrów z systemem testu obciążenia. Jeśli konfigurujesz usługę klastra dla obciążeń produkcyjnych, kontynuuj odczytu.

#### <a name="cluster-and-server-certificate-required"></a>Klaster a serwera certyfikatów (wymagane)
Ten certyfikat jest wymagany do zabezpieczania klastra i uniemożliwić nieautoryzowany dostęp do niego. Udostępnia ona zabezpieczeń klastra na kilka sposobów:

* **Uwierzytelnianie klastra:** uwierzytelnia komunikacji między węzłami w Federacji klastra. Tylko węzły, które można potwierdzić swoją tożsamość, z tym certyfikatem można dołączyć do klastra.
* **Uwierzytelnianie serwera:** uwierzytelnia punktów końcowych klastra zarządzania do klienta zarządzania, aby klient zarządzania znała rozmawia rzeczywistych klastra. Ten certyfikat zapewnia również SSL interfejsu API zarządzania HTTPS oraz Service Fabric Explorer, za pośrednictwem protokołu HTTPS.

Do obsługi tych celów, certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzony dla wymiany kluczy, można eksportować do pliku wymiany informacji osobistych (pfx).
* Certyfikat **nazwa podmiotu musi być zgodny z domeną** umożliwiają dostęp do klastra usługi sieć szkieletowa usług. Jest to wymagane jest podanie protokołu SSL dla punktów końcowych HTTPS zarządzania i Service Fabric Explorer klastra. Nie można uzyskać certyfikat od urzędu certyfikacji (CA) dla `.cloudapp.azure.com` domeny. Możliwość nabycia niestandardowej nazwy domeny dla klastra. Gdy żądanie certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny niestandardowej, używane dla klastra.

#### <a name="client-authentication-certificates"></a>Certyfikaty uwierzytelniania klienta
Certyfikaty klienta dodatkowe uwierzytelnianie Administratorzy dla zadań zarządzania w klastrze. Sieć szkieletowa usług ma dwa poziomy dostępu: **admin** i **użytkownika tylko do odczytu**. Co najmniej jeden certyfikat dla dostępu administracyjnego należy używać. Dodatkowe dostępu na poziomie użytkownika należy podać oddzielny certyfikat. Aby uzyskać więcej informacji na dostęp do ról, zobacz [kontroli dostępu opartej na rolach dla klientów usługi sieć szkieletowa][service-fabric-cluster-security-roles].

Nie należy przekazywać certyfikatów uwierzytelniania klienta do usługi Key Vault do pracy z sieci szkieletowej usług. Tylko te certyfikaty należy podać użytkownikom, którzy mają uprawnienia do zarządzania klastrem. 

> [!NOTE]
> Azure Active Directory jest zalecanym sposobem uwierzytelniania klientów dla operacji zarządzania klastrem. Aby korzystać z usługi Azure Active Directory, należy najpierw [Tworzenie klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów można zainstalować w klastrze dla aplikacji ze względów bezpieczeństwa. Przed utworzeniem klastra, należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają certyfikatów do zainstalowania w węzłach, takich jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji
* Szyfrowanie danych w węzłach podczas replikacji 

Nie można skonfigurować certyfikaty aplikacji, podczas tworzenia klastra za pośrednictwem portalu Azure. Aby skonfigurować certyfikaty aplikacji podczas konfiguracji klastra, należy najpierw [Tworzenie klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm]. Można również dodać certyfikatów aplikacji do klastra, po jego utworzeniu.

< /a "Tworzenie klastra portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Tworzenie klastra w portalu Azure

Tworzenia klastra produkcyjnego dostosowane do potrzeb aplikacji obejmuje niektóre planowania ułatwiające ze specyfikacją, zdecydowanie zaleca się przeczytać i zrozumieć [klastra sieci szkieletowej usług zagadnienia związane z planowaniem] [ service-fabric-cluster-capacity] dokumentu. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Wyszukaj zasobu klastra usługi sieć szkieletowa
![Wyszukiwanie szablonu klastra sieci szkieletowej usług w portalu Azure.][SearchforServiceFabricClusterTemplate]

1. Zaloguj się w witrynie [Azure Portal][azure-portal].
2. Kliknij przycisk **Utwórz zasób** można dodać nowego szablonu zasobów. Wyszukaj szablonu klastra sieci szkieletowej usług w **Marketplace** w obszarze **wszystko**.
3. Wybierz **klastra sieci szkieletowej usług** z listy.
4. Przejdź do **klastra sieci szkieletowej usług** bloku, kliknij przycisk **Utwórz**,
5. **Klastra tworzenia sieci szkieletowej usług** bloku obejmuje następujące cztery kroki:

#### <a name="1-basics"></a>1. Podstawy
![Zrzut ekranu przedstawiający tworzenie nowej grupy zasobów.][CreateRG]

W bloku podstawowe należy podać podstawowe szczegóły dla klastra.

1. Wprowadź nazwę klastra.
2. Wprowadź **nazwy użytkownika** i **hasło** dla pulpitu zdalnego dla maszyn wirtualnych.
3. Upewnij się wybrać **subskrypcji** mają klastra do wdrożenia, zwłaszcza, jeśli masz wiele subskrypcji.
4. Utwórz **nową grupę zasobów**. Zaleca się nadać mu taką samą nazwę jak klastra, ponieważ pomaga w znajdowaniu je później, szczególnie w przypadku, gdy chcesz wprowadzić zmiany do wdrożenia lub usunąć klaster.
   
   > [!NOTE]
   > Mimo że można użyć istniejącej grupy zasobów, jest dobrym rozwiązaniem, aby utworzyć nową grupę zasobów. Dzięki temu można łatwo usunąć klastrów i wszystkich zasobów, które są używane.
   > 
   > 
5. Wybierz **region** , w której chcesz utworzyć klaster. Jeśli planujesz użyć istniejącego certyfikatu, które już zostały przekazane do magazynu kluczy, należy użyć tego samego regionu, który jest magazyn kluczy. 

#### <a name="2-cluster-configuration"></a>2. Konfiguracja klastra
![Tworzenie typu węzła][CreateNodeType]

Skonfiguruj węzły klastra. Typy węzłów zdefiniować rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Klaster może mieć więcej niż jeden typ węzła, ale typ węzła podstawowego (pierwszego zdefiniowanego w portalu) musi mieć co najmniej pięć maszyn wirtualnych, jak jest to typ węzła rozmieszczenia usługi sieć szkieletowa usług systemowych. Nie należy konfigurować **właściwości umieszczania** ponieważ domyślna właściwość umieszczania "NodeTypeName" są dodawane automatycznie.

> [!NOTE]
> Typowy scenariusz dla wielu typów węzła jest aplikacja, która zawiera usługi frontonu i zaplecza. Chcesz umieścić usługi frontonu na mniejsze maszyn wirtualnych (takie jak D2_V2 rozmiarów maszyn wirtualnych) z otwartych portów z Internetem, a usługi zaplecza na maszynach wirtualnych większych (z rozmiarów maszyn wirtualnych, takie jak D3_V2, D6_V2 D15_V2 i tak dalej) z żadnych portów skierowane do Internetu Otwórz.
> 
> 

1. Wybierz nazwę dla danego typu węzła (od 1 do 12 znaków zawierających tylko litery i cyfry).
2. Minimalna **rozmiar** maszyn wirtualnych dla węzła podstawowego typu jest wymuszany przez **trwałości** warstwy wybierz dla klastra. Wartość domyślna warstwa trwałości to brązowa. Aby uzyskać więcej informacji o trwałości, zobacz [Wybieranie trwałości klastra usługi sieć szkieletowa][service-fabric-cluster-durability].
3. Wybierz rozmiar maszyny Wirtualnej. Maszyny wirtualne D-series mają dyski SSD i zaleca aplikacji stanowych. Użyj dowolnej jednostki SKU maszyny Wirtualnej z częściowa rdzeni lub nie mają mniej niż 10 GB pojemności dysku. Zapoznaj się [klastra sieci szkieletowej usług planowania dokumentu brany pod uwagę] [ service-fabric-cluster-capacity] Aby uzyskać pomoc przy wyborze rozmiar maszyny Wirtualnej.
4. Wybierz liczbę maszyn wirtualnych dla typu węzła. Możesz skalować w górę lub w dół liczbę maszyn wirtualnych w typ węzła później, ale dla typu węzła podstawowego minimum jest pięć dla obciążeń produkcyjnych. Inne typy węzłów może mieć co najmniej jedną maszynę Wirtualną. Minimalna **numer** maszyn wirtualnych dla dysków typu węzła podstawowego **niezawodności** klastra.  
5. **Pojedynczy węzeł klastra i trzy klastry węzłów** — te są przeznaczone tylko dla testu. Nie są obsługiwane dla dowolnego uruchomionych obciążeń produkcyjnych.
6. Skonfiguruj niestandardowe punkty końcowe. To pole umożliwia wprowadź rozdzielaną przecinkami listę portów, które chcesz udostępnić za pośrednictwem usługi równoważenia obciążenia Azure do publicznego Internetu dla aplikacji. Na przykład jeśli planujesz wdrożyć aplikację sieci web do klastra, wprowadź "80" w tym miejscu zezwalająca na ruch na porcie 80 do klastra. Aby uzyskać więcej informacji dotyczących punktów końcowych, zobacz [komunikacji z aplikacjami][service-fabric-connect-and-communicate-with-services]
7. Konfigurowanie klastra **diagnostyki**. Domyślnie diagnostyki są włączone w klastrze celu ułatwienia rozwiązywania problemów. Jeśli chcesz wyłączyć zmiany diagnostyki **stan** Przełącz, aby **poza**. Wyłączenie diagnostyki jest **nie** zalecane. Jeśli masz już utworzonego projektu usługi Application Insights, następnie podaj jego klucza, aby śladów aplikacji są kierowane do niego.
8. Wybierz tryb uaktualniania sieci szkieletowej ma ustawioną wartość klastra. Wybierz **automatyczne**, jeśli system ma automatycznie Podnieś najnowszej dostępnej wersji i spróbuj uaktualnić klaster do niego. Ustaw tryb **ręcznego**, jeśli chcesz wybrać obsługiwanej wersji. Więcej informacji na temat sieci szkieletowej uaktualnić tryb zobacz [dokumentu usługi sieci szkieletowej klastra uaktualnienia.][service-fabric-cluster-upgrade]

> [!NOTE]
> Firma Microsoft obsługuje tylko klastry, które są z obsługiwanymi wersjami usługi sieci szkieletowej. Wybierając **ręcznego** trybie podjęcia na odpowiedzialność do uaktualnienia klastra do obsługiwanej wersji. > 
> 

#### <a name="3-security"></a>3. Bezpieczeństwo
![Zrzut ekranu przedstawiający konfiguracjach zabezpieczeń w portalu Azure.][BasicSecurityConfigs]

Aby ułatwić skonfigurowanie klastra bezpiecznego testu dla Ciebie, firma Microsoft umieściła **podstawowe** opcji. Jeśli możesz już ma certyfikat i przekazać go do Twojego keyvault (i włączone magazynu kluczy dla wdrożenia), użyj **niestandardowy** opcji

##### <a name="basic-option"></a>Opcja podstawowe
Wykonaj ekranów w celu dodania lub ponowne użycie istniejących keyvault i dodawanie certyfikatu. Dodawanie certyfikatu jest procesem synchroniczne i dlatego trzeba będzie czekać na certyfikat, który ma zostać utworzony.


Oprzeć możliwość przesłania nawigacji po ekranie, aż do zakończenia poprzedniego procesu.

![CreateKeyVault]

Teraz, certyfikatu zostanie dodany do Twojej keyvault, może zostać wyświetlony następujący ekran monitowania użytkownika można edytować zasady dostępu dla użytkownika Keyvault. polecenie **edycji zasad dostępu.** Dodaj...

![CreateKeyVault2]

Kliknij zasady dostępu zaawansowane i włączyć dostęp do maszyn wirtualnych do wdrożenia. Zaleca się włączenie również wdrażania szablonu. Po dokonaniu wybrane elementy, nie zapomnij kliknij **zapisać** przycisk i zamknij poza **zasady dostępu** okienka.

![CreateKeyVault3]

Teraz można przystąpić do przejścia do pozostałej części procesu tworzenia klastra.

![CreateKeyVault4]

##### <a name="custom-option"></a>Opcja Niestandardowa
Pomiń tę sekcję, jeśli wykonano już kroki opisane w **podstawowe** opcji.

![SecurityCustomOption]

Należy CertificateThumbprint, SourceVault i informacje CertificateURL, aby ukończyć strony zabezpieczeń. Jeśli nie ma on przydatny, otworzyć inne okno przeglądarki i wykonaj następujące czynności


1. Przejdź do Twojej keyvault, wybrać certyfikat. 
2. Wybierz kartę "właściwości" i skopiuj identyfikator ZASOBU do "Magazyn kluczy źródło" w oknie przeglądarki 

    ![CertInfo0]

3. Teraz wybierz kartę "Certyfikaty".
4. Polecenie odcisk palca certyfikatu, który prowadzi do strony wersji.
5. Polecenie identyfikatory GUID wyświetlony w bieżącej wersji.

    ![CertInfo1]

6. Teraz należy na ekranie, podobnie jak poniżej. Skopiuj odcisk palca do "Odcisk palca certyfikatu" w oknie przeglądarki
7. Skopiuj informacje "Klucz tajny identyfikator" do "adres URL certyfikatu" w innym oknie przeglądarki.


![CertInfo2]


Sprawdź **Skonfiguruj ustawienia zaawansowane** pole służące do wprowadzania certyfikaty klientów dla **klienta administrator** i **klienta tylko do odczytu**. W tych polach wprowadź odcisk palca certyfikatu klienta administratora i odcisk palca certyfikatu klienta użytkownika tylko do odczytu, jeśli ma to zastosowanie. Gdy administratorzy próbować połączyć się z klastrem, otrzymali dostępu tylko wtedy, gdy mają certyfikat z odciskiem palca, który odpowiada wartości odcisku palca wprowadzanym w tym miejscu.  

#### <a name="4-summary"></a>4. Podsumowanie

Teraz można przystąpić do wdrażania klastra. Przed tym, Pobierz certyfikat, poszukać wewnątrz dużych niebieski pola informacyjny dla łącza. Upewnij się zachować certyfikat w bezpiecznym miejscu. należy go do nawiązania połączenia z klastrem. Ponieważ certyfikat, który został pobrany nie ma hasła, zalecane jest dodanie jednego.

Aby zakończyć tworzenie klastra, kliknij przycisk **Utwórz**. Opcjonalnie można pobrać szablonu. 

![Podsumowanie]

Możesz zobaczyć postępy tworzenia w powiadomieniach. (Kliknij ikonę „Dzwonka” w pobliżu paska stanu w prawym górnym rogu ekranu). Jeśli kliknięto opcję **Przypnij do tablicy startowej** podczas tworzenia klastra, zobaczysz pozycję **Wdrażanie klastra usługi Service Fabric** przypiętą do tablicy **Start**.

Aby wykonywać operacje zarządzania w klastrze za pomocą programu Powershell lub interfejsu wiersza polecenia, należy połączyć się z klastrem, Dowiedz się więcej na temat sposobu na [nawiązywania połączenia z klastrem](service-fabric-connect-to-secure-cluster.md).

### <a name="view-your-cluster-status"></a>Wyświetl stan sieci klastra
![Zrzut ekranu przedstawiający szczegółów klastra na pulpicie nawigacyjnym.][ClusterDashboard]

Po utworzeniu klastra można sprawdzić w portalu klastra:

1. Przejdź do **Przeglądaj** i kliknij przycisk **klastry usługi sieć szkieletowa**.
2. Znajdź klastra i kliknij ją.
3. Możesz teraz wyświetlić szczegóły klastra na pulpicie nawigacyjnym, w tym publiczny punkt końcowy klastra oraz link do narzędzia Service Fabric Explorer.

**Monitor węzła** sekcji w bloku pulpit nawigacyjny klastra wskazuje liczbę maszyn wirtualnych, które są w dobrej kondycji i nie działa prawidłowo. Można znaleźć więcej szczegółów na temat kondycji klastra na [wprowadzenie modelu kondycji sieci szkieletowej usług][service-fabric-health-introduction].

> [!NOTE]
> Klastry usługi sieć szkieletowa usług wymagają określonej liczby węzłów pozwala uzyskać się zawsze zachować dostępność i zachować stan — nazywany "utrzymywania kworum". W związku z tym zazwyczaj nie jest bezpiecznie wyłączyć wszystkie komputery w klastrze, chyba że najpierw wykonali [pełnej kopii zapasowej według stanu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Zdalnego łączenia się z wystąpieniem zestawu skalowania maszyn wirtualnych lub węzła klastra
Każdy z elementów NodeType, określ w wynikach klastra w zestawu skalowania maszyn wirtualnych pobieranie konfiguracji. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz bezpiecznego klastra przy użyciu certyfikatów dla uwierzytelniania zarządzania. Następnie [połączyć się z klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak [Zarządzanie klucze tajne aplikacji](service-fabric-application-secret-management.md).  Zapoznaj się z [opcje pomocy technicznej usługi sieć szkieletowa](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Podsumowanie]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
