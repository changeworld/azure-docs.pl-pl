---
title: Tworzenie klastra usługi Service Fabric w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bezpieczny klaster usługi Service Fabric na platformie Azure przy użyciu witryny Azure portal i usługa Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 02312a19c687908b0e1c0e6417dc6b0a9df23912
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669473"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Tworzenie klastra usługi Service Fabric na platformie Azure przy użyciu witryny Azure portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Jest to przewodnik krok po kroku, który przeprowadzi Cię przez kroki konfigurowania klastra usługi Service Fabric (Linux lub Windows) na platformie Azure przy użyciu witryny Azure portal. Ten przewodnik przeprowadzi Cię przez następujące kroki:

* Tworzenie klastra na platformie Azure za pośrednictwem witryny Azure portal.
* Uwierzytelnianie przy użyciu certyfikatów administratorów.

> [!NOTE]
> Aby uzyskać więcej informacji o zaawansowanych opcji zabezpieczeń, takich jak uwierzytelnianie użytkowników za pomocą usługi Azure Active Directory oraz konfigurowania certyfikaty dla zabezpieczeń aplikacji [Tworzenie klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpieczenia klastra 
W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Aby uzyskać więcej informacji na temat używania certyfikatów w usłudze Service Fabric, zobacz [scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security].

Jeśli po raz pierwszy tworzysz klaster usługi Service fabric lub wdrażania klastra dla obciążeń testowych, możesz przejść do następnej sekcji (**Tworzenie klastra w witrynie Azure Portal**) i generowania certyfikatów wymaganych podczas w systemie klastrów działających obciążeń testowych. Jeśli przeprowadzasz konfigurację klastra dla obciążeń produkcyjnych, następnie kontynuuj czytanie.

#### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagane)
Ten certyfikat jest wymagany do zabezpieczania klastra i zapobiegać nieautoryzowanemu dostępowi do niego. Oferuje ono zabezpieczenia klastra na kilka sposobów:

* **Uwierzytelnianie klastra:** Uwierzytelnia komunikacji między węzłami dla Federacji klastra. Tylko węzły, które można potwierdzić swoją tożsamość za pomocą tego certyfikatu można dołączyć do klastra.
* **Uwierzytelnianie serwera:** Uwierzytelnia punktów końcowych zarządzania klastrem na kliencie zarządzania tak, aby klient zarządzania wie, że jest ona rozmawiając z rzeczywistym klastrem. Ten certyfikat zapewnia również protokół SSL dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer, za pośrednictwem protokołu HTTPS.

Do realizacji konkretnych celów, certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzona na potrzeby wymiany kluczy, który można eksportować do pliku wymiany informacji osobistych (pfx).
* Certyfikat **nazwa podmiotu musi odpowiadać domeny** umożliwiający dostęp do klastra usługi Service Fabric. Jest to wymagane, aby zapewnić protokół SSL dla punktów końcowych zarządzania protokołu HTTPS klastra i narzędzia Service Fabric Explorer. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji (CA) dla `.cloudapp.azure.com` domeny. Uzyskać niestandardową nazwę domeny dla klastra. Żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi odpowiadać niestandardowej nazwy domeny używane dla klastra.

#### <a name="client-authentication-certificates"></a>Certyfikaty uwierzytelniania klienta
Dodatkowych certyfikatów klienta uwierzytelniania administratorów dla zadań zarządzania klastrem. Usługa Service Fabric zawiera dwa poziomy dostępu: **administratora** i **użytkownika tylko do odczytu**. Co najmniej jeden certyfikat dla dostępu administracyjnego należy używać. Aby uzyskać dodatkowe dostępu na poziomie użytkownika należy podać oddzielny certyfikat. Aby uzyskać więcej informacji na temat dostępu do ról, zobacz [kontroli dostępu opartej na rolach dla klientów usługi Service Fabric][service-fabric-cluster-security-roles].

Nie trzeba przekazywać certyfikatów uwierzytelniania klienta do usługi Key Vault do pracy z usługą Service Fabric. Te certyfikaty należy tylko podać dla użytkowników, którzy są autoryzowane do zarządzania klastrem. 

> [!NOTE]
> Usługa Azure Active Directory jest zalecanym sposobem uwierzytelniania klientów na potrzeby operacji zarządzania klastrem. Aby korzystać z usługi Azure Active Directory, musisz mieć [Tworzenie klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów można zainstalować w klastrze ze względów bezpieczeństwa aplikacji. Przed utworzeniem klastra, należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają certyfikatów do zainstalowania w węzłach, takich jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji
* Szyfrowanie danych między węzłami podczas replikacji 

Certyfikaty aplikacji nie może być skonfigurowany, kiedy [tworzenia klastra za pośrednictwem witryny Azure portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Aby skonfigurować certyfikaty aplikacji podczas konfiguracji klastra, należy najpierw [Tworzenie klastra przy użyciu usługi Azure Resource Manager][create-cluster-arm]. Możesz również dodać certyfikatów aplikacji do klastra po jego utworzeniu.

## <a name="create-cluster-in-the-azure-portal"></a>Tworzenie klastra w witrynie Azure portal

Tworzenie klastra produkcyjnego, aby spełnić wymagania aplikacji obejmuje pewne planowanie do udzielenia odpowiedzi na który, zdecydowanie zaleca się należy przeczytać i sprawdzić [klastra usługi Service Fabric zagadnienia związane z planowaniem] [ service-fabric-cluster-capacity] dokumentu. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Wyszukaj zasób klastra usługi Service Fabric

Zaloguj się w witrynie [Azure Portal][azure-portal].
Kliknij przycisk **Utwórz zasób** można dodać nowego szablonu zasobów. Wyszukaj szablon klastra usługi Service Fabric w **Marketplace** w obszarze **wszystko**.
Wybierz **klastra usługi Service Fabric** z listy.

![Wyszukaj szablon klastra usługi Service Fabric w witrynie Azure portal.][SearchforServiceFabricClusterTemplate]

Przejdź do **klastra usługi Service Fabric** bloku i kliknij przycisk **Utwórz**.

**Klastra Utwórz usługi Service Fabric** blok zawiera następujące cztery kroki:

### <a name="1-basics"></a>1. Podstawy
![Zrzut ekranu przedstawiający tworzenie nowej grupy zasobów.][CreateRG]

W bloku podstawowe należy podać podstawowe szczegóły klastra.

1. Wprowadź nazwę klastra.
2. Wprowadź **nazwa_użytkownika** i **hasło** za pomocą pulpitu zdalnego dla maszyn wirtualnych.
3. Upewnij się, że wybrano **subskrypcji** zamiaru klastra do wdrożenia, zwłaszcza, jeśli masz wiele subskrypcji.
4. Utwórz nową **grupy zasobów**. Warto nadać mu taką samą nazwę jak klastra, ponieważ pomaga w znalezieniu je później, szczególnie w przypadku, gdy chcesz wprowadzić zmiany do wdrożenia lub usunąć klaster.
   
   > [!NOTE]
   > Mimo, że użytkownik chce użyć istniejącej grupy zasobów, jest dobrym rozwiązaniem, aby utworzyć nową grupę zasobów. Dzięki temu można łatwo usuwać klastry i wszystkie zasoby, które są używane.
   > 
   > 
5. Wybierz **lokalizacji** , w której chcesz utworzyć klaster. Jeśli planujesz użyć istniejącego certyfikatu, które już zostały przekazane do magazynu kluczy, należy użyć tego samego regionu, który znajduje się w usłudze Key vault. 

### <a name="2-cluster-configuration"></a>2. Konfiguracja klastra
![Tworzenie typu węzła][CreateNodeType]

Skonfiguruj węzły klastra. Typy węzłów definiują rozmiarów maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła (pierwszego zdefiniowanemu w portalu) musi mieć co najmniej pięć maszyn wirtualnych, ponieważ jest typem węzła rozmieszczenie usługi systemowe Service Fabric. Nie należy konfigurować **właściwości umieszczania** ponieważ domyślna właściwość umieszczania "NodeTypeName" jest automatycznie dodawany.

> [!NOTE]
> Typowy scenariusz dla wielu typów węzła jest aplikacja, która zawiera usługę frontonu i usługi zaplecza. Chcesz umieścić usługi frontonu na mniejszych maszynach wirtualnych (takich jak D2_V2 rozmiarów maszyn wirtualnych) z otwartych portów w Internecie, a usługi zaplecza na większych maszyn wirtualnych (przy użyciu rozmiarów maszyn wirtualnych, takich jak D3_V2, D6_V2, D15_V2 i tak dalej) za pomocą nie otwartych portów dostępnego z Internetu.
> 

1. Wybierz nazwę typu węzła (od 1 do 12 znaków, zawierającą tylko litery i cyfry).
2. Minimalna **rozmiar** maszyn wirtualnych dla węzła podstawowego typu jest wymuszany przez **warstwa trwałości** wybierz dla klastra. Wartość domyślna warstwa trwałości jest brązowa. Aby uzyskać więcej informacji o trwałości, zobacz [sposobu wybierania trwałości klastra usługi Service Fabric][service-fabric-cluster-durability].
3. Wybierz **rozmiar maszyny wirtualnej**. Maszyny wirtualne z serii D mają dyski SSD i są wysoce zalecane w przypadku aplikacji stanowych. Użyj jednostki SKU maszyny Wirtualnej, zawierający częściowe rdzeni lub nie masz mniej niż 10 GB dostępnego miejsca na dysku pojemności. Zapoznaj się [klastra usługi Service fabric brany pod uwagę dokumentu planowania] [ service-fabric-cluster-capacity] Aby uzyskać pomoc, wybierając rozmiar maszyny Wirtualnej.
4.  **Pojedyncze węzły klastra i trzech węzłów klastrów** są przeznaczone tylko dla testu. Nie są obsługiwane dla dowolnych uruchomionych obciążeń produkcyjnych.
5. Wybierz **pojemność zestawu skalowania maszyn wirtualnych początkowej** dla typu węzła. Możesz skalować w górę lub w dół liczbę maszyn wirtualnych w typ węzła później, ale na podstawowy typ węzła, wartość minimalna to 5 dla obciążeń produkcyjnych. Inne typy węzłów może mieć co najmniej jedną maszynę Wirtualną. Minimalna **numer** maszyn wirtualnych dla dysków typu węzła podstawowego **niezawodność** klastra.  
6. Konfigurowanie **niestandardowe punkty końcowe**. To pole pozwala wprowadzić przecinkami lista portów, które chcesz udostępnić za pośrednictwem usługi Azure Load Balancer do publicznego Internetu dla aplikacji. Na przykład jeśli planujesz wdrożyć aplikację sieci web w klastrze, wprowadź "80" tutaj aby zezwolić na ruch na porcie 80 w klastrze. Aby uzyskać więcej informacji na temat punktów końcowych, zobacz [podczas komunikowania się z aplikacjami][service-fabric-connect-and-communicate-with-services]
7. **Włącz zwrotny serwer proxy**.  [Usługi Service Fabric zwrotny serwer proxy](service-fabric-reverseproxy.md) mikrousług pomaga działającego w klastrze usługi Service Fabric odnajdywania i komunikować się z innymi usługami, które mają punktów końcowych http.
8. Ponownie **konfiguracji klastra** bloku, w obszarze **+ Pokaż ustawienia opcjonalne**, skonfigurować klaster **diagnostyki**. Domyślnie diagnostyki są włączone w klastrze na potrzeby rozwiązywania problemów. Jeśli chcesz wyłączyć zmiany diagnostyki **stan** Przełącz, aby **poza**. Wyłączenie diagnostyki jest **nie** zalecane. Jeśli już masz utworzonego projektu usługi Application Insights, a następnie przekazać jego klucza śladów aplikacji są kierowane do niego.
9. **Uwzględnij usługę DNS**.  [Usługi DNS](service-fabric-dnsservice.md) to opcjonalna usługa, która umożliwia znalezienie innych usług za pomocą protokołu DNS.
10. Wybierz **tryb uaktualniania sieci szkieletowej** ma równa klastra. Wybierz **automatyczne**, jeśli system ma automatycznie wybierze najnowszej dostępnej wersji i spróbuj uaktualnić klaster do niego. Ustaw tryb **ręczne**, jeśli chcesz wybrać obsługiwaną wersję. Szczegółowe informacje na temat sieci szkieletowej uaktualnić tryb zobacz [dokumentu uaktualnienie do klastra usługi Service Fabric.][service-fabric-cluster-upgrade]

> [!NOTE]
> Obsługiwane są tylko klastry z systemem obsługiwanych wersji usługi Service Fabric. Wybierając **ręczne** trybie są przełączania się na odpowiedzialność do uaktualnienia klastra do obsługiwanej wersji.
> 

### <a name="3-security"></a>3. Bezpieczeństwo
![Zrzut ekranu przedstawiający konfiguracji zabezpieczeń w witrynie Azure portal.][BasicSecurityConfigs]

Aby ułatwić konfigurowanie klastra testowego bezpieczny dla Ciebie, zostały zamieszczone **podstawowe** opcji. Jeśli możesz już mieć certyfikat i przekazano go do Twojego [usługi key vault](/azure/key-vault/) (i włączyć usługi key vault dla wdrożenia), następnie za pomocą **niestandardowe** opcji

#### <a name="basic-option"></a>Opcja podstawowa
Postępuj zgodnie z ekranów w celu dodawania lub ponowne użycie istniejącego magazynu kluczy i dodawanie certyfikatu. Dodanie certyfikatu jest procesie synchronicznym i dlatego trzeba będzie czekać na certyfikat, który ma zostać utworzony.

Siłowym możliwość przesłania o przejściu na ekranie, aż do zakończenia poprzedniego procesu.

![CreateKeyVault]

Teraz, gdy zostanie utworzony magazyn kluczy, Edytuj zasady dostępu dla magazynu kluczy. 

![CreateKeyVault2]

Kliknij pozycję **Edytuj zasady dostępu**, następnie **Pokaż zaawansowane zasady dostępu** i włączyć dostęp do usługi Azure Virtual Machines na potrzeby wdrożenia. Zalecane jest, aby włączyć również wdrożenia szablonu. Po dokonaniu wyborów, nie zapomnij kliknąć **Zapisz** przycisk, a następnie zamknij poza **zasady dostępu** okienka.

![CreateKeyVault3]

Wprowadź nazwę certyfikatu, a następnie kliknij przycisk **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Opcja Niestandardowa
Jeśli wykonano już kroki opisane w pominąć tę sekcję **podstawowe** opcji.

![SecurityCustomOption]

Należy źródłowy magazyn kluczy, adres URL certyfikatu i informacji o odcisku palca certyfikatu, aby ukończyć strony zabezpieczeń. Jeśli nie jest przydatna, otworzyć inne okno przeglądarki i w witrynie Azure portal, wykonaj następujące czynności

1. Przejdź do usługi magazynu kluczy.
2. Wybierz kartę "Properties", a następnie skopiuj identyfikator ZASOBU do "Źródłowy magazyn kluczy" w oknie przeglądarki 

    ![CertInfo0]

3. Teraz wybierz kartę "Certyfikaty".
4. Polecenie odcisk palca certyfikatu, który prowadzi do strony wersji.
5. Polecenie identyfikatory GUID, zostanie wyświetlony w bieżącej wersji.

    ![CertInfo1]

6. Teraz należy na ekranie, takich jak poniżej. Skopiuj szesnastkowy odcisk palca SHA-1 "Odcisk palca certyfikatu" w oknie przeglądarki
7. Skopiuj "Identyfikator wpisu tajnego" do "Certyfikat adresu URL" w innym oknie przeglądarki.

    ![CertInfo2]

Sprawdź **Skonfiguruj ustawienia zaawansowane** pole, aby wprowadzić certyfikatów klienta dla **Klient administracyjny** i **klienta tylko do odczytu**. W tych polach wprowadź odcisk palca certyfikatu klienta administratora i odcisk palca certyfikatu klienta użytkownika tylko do odczytu, jeśli ma to zastosowanie. Gdy administratorzy próbują nawiązać połączenie z klastrem, otrzymują dostęp tylko wtedy, gdy mają certyfikat z odciskiem palca, który pasuje do wartości odcisku palca wprowadzone w tym miejscu.  

### <a name="4-summary"></a>4. Podsumowanie

Teraz wszystko jest gotowe do wdrożenia klastra. Zanim to zrobisz, Pobierz certyfikat, poszukaj w dużych niebieskie pole informacyjne łącza. Upewnij się zachować certyfikat w bezpiecznym miejscu. Możesz go potrzebować, aby nawiązać połączenie z klastrem. Ponieważ certyfikat, który został pobrany, nie ma hasła, zalecane jest dodanie jednego.

Aby ukończyć tworzenia klastra, kliknij przycisk **Utwórz**. Opcjonalnie można pobrać szablonu.

![Podsumowanie]

Możesz zobaczyć postępy tworzenia w powiadomieniach. (Kliknij ikonę „Dzwonka” w pobliżu paska stanu w prawym górnym rogu ekranu). Jeśli kliknięto opcję **Przypnij do tablicy startowej** podczas tworzenia klastra, zobaczysz pozycję **Wdrażanie klastra usługi Service Fabric** przypiętą do tablicy **Start**. Ten proces wymaga czasu. 

W celu wykonywania operacji zarządzania w klastrze przy użyciu programu Powershell lub interfejsu wiersza polecenia, należy połączyć się z klastrem, Dowiedz się więcej na temat sposobu na [nawiązywania połączenia z klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Wyświetl swój stan klastra
![Zrzut ekranu przedstawiający szczegóły klastra na pulpicie nawigacyjnym.][ClusterDashboard]

Po utworzeniu klastra możesz sprawdzić klaster w portalu:

1. Przejdź do **Przeglądaj** i kliknij przycisk **klastry usługi Service Fabric**.
2. Zlokalizuj klaster, a następnie kliknij go.
3. Możesz teraz wyświetlić szczegóły klastra na pulpicie nawigacyjnym, w tym publiczny punkt końcowy klastra oraz link do narzędzia Service Fabric Explorer.

**Monitor węzła** sekcji bloku pulpit nawigacyjny klastra wskazuje liczbę maszyn wirtualnych, które są w dobrej kondycji i nie działa prawidłowo. Można znaleźć szczegółowe informacje o kondycji klastra [wprowadzenie modelu kondycji usługi Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Klastry usługi Service Fabric wymagają określonej liczby węzłów, aby rozpocząć zawsze do zapewnienia dostępności i zachować stan — określane jako "utrzymania kworum". W związku z tym, zwykle nie jest bezpieczne do zamykania wszystkich maszyn w klastrze, o ile nie zostało przeprowadzone [pełną kopię zapasową stanu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne wystąpienie zestawu skalowania maszyn wirtualnych lub węzła klastra
Każdy z elementy NodeType, określ w wynikach w zestawie skalowania maszyn wirtualnych pobieranie konfiguracji klastra. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz zabezpieczonego klastra za pomocą certyfikatów do uwierzytelniania zarządzania. Następnie [połączenia z klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak [Zarządzanie wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).  Ponadto Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
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
