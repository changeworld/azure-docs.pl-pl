---
title: Konfigurowanie bezpiecznych połączeń klastra sieci szkieletowej usług Azure
description: Dowiedz się, jak korzystać z programu Visual Studio do konfigurowania bezpiecznych połączeń obsługiwanych przez klaster sieci szkieletowej usług Azure.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464096"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurowanie bezpiecznych połączeń z klastrem sieci szkieletowej usług z programu Visual Studio
Dowiedz się, jak bezpiecznie uzyskać dostęp do klastra sieci szkieletowej usług Azure z skonfigurowaną zasadą kontroli dostępu za pomocą programu Visual Studio.

## <a name="cluster-connection-types"></a>Typy połączeń klastra
Klastra sieci szkieletowej usług Azure obsługuje dwa typy połączeń: **niezabezpieczone** połączenia i bezpieczne połączenia **oparte na certyfikatach x509.** (W przypadku klastrów sieci szkieletowej usług hostowanych lokalnie obsługiwane są również uwierzytelnianie **systemu Windows** i **dSTS).** Podczas tworzenia klastra należy skonfigurować typ połączenia klastra. Po utworzeniu nie można zmienić typu połączenia.

Narzędzia sieci szkieletowej usług Visual Studio obsługują wszystkie typy uwierzytelniania w celu nawiązania połączenia z klastrem w celu opublikowania. Zobacz [Konfigurowanie klastra sieci szkieletowej usług z witryny Azure portal,](service-fabric-cluster-creation-via-portal.md) aby uzyskać instrukcje dotyczące konfigurowania bezpiecznego klastra sieci szkieletowej usług.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurowanie połączeń klastra w profilach publikowania
Jeśli publikujesz projekt sieci szkieletowej usług z programu Visual Studio, użyj okna dialogowego **Publikowanie aplikacji sieci** szkieletowej usługi, aby wybrać klaster sieci szkieletowej usług Azure. W obszarze **Punkt końcowy połączenia**wybierz istniejący klaster w ramach subskrypcji.

![Okno dialogowe **Publikowanie aplikacji sieci szkieletowej usługi** służy do konfigurowania połączenia sieci szkieletowej usług.][publishdialog]

Okno dialogowe **Publikowanie aplikacji sieci szkieletowej usługi** automatycznie sprawdza poprawność połączenia klastra. Jeśli zostanie wyświetlony monit, zaloguj się do konta platformy Azure. Jeśli sprawdzanie poprawności przebiega pomyślnie, oznacza to, że system ma zainstalowane odpowiednie certyfikaty, aby bezpiecznie połączyć się z klastrem lub klaster jest niezabezpieczony. Błędy sprawdzania poprawności mogą być spowodowane problemami z siecią lub brakiem prawidłowego skonfigurowania systemu do łączenia się z bezpiecznym klastrem.

![Okno dialogowe **Publikowanie aplikacji sieci szkieletowej usługi** sprawdza poprawność istniejącego, poprawnie skonfigurowanego połączenia klastra sieci szkieletowej usług.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Aby połączyć się z bezpiecznym klastrem
1. Upewnij się, że masz dostęp do jednego z certyfikatów klienta, którym ufa klaster docelowy. Certyfikat jest zwykle udostępniany jako plik wymiany informacji osobistych (pfx). Zobacz [Konfigurowanie klastra sieci szkieletowej usług z witryny Azure portal,](service-fabric-cluster-creation-via-portal.md) aby dowiedzieć się, jak skonfigurować serwer w celu udzielenia dostępu klientowi.
2. Zainstaluj zaufany certyfikat. W tym celu kliknij dwukrotnie plik pfx lub użyj skryptu Programu PowerShell Import-PfxCertificate, aby zaimportować certyfikaty. Zainstaluj certyfikat w **cercie:\LocalMachine\My**. Można zaakceptować wszystkie ustawienia domyślne podczas importowania certyfikatu.
3. Wybierz polecenie **Publikuj...** w menu skrótów projektu, aby otworzyć okno dialogowe **Publikowanie aplikacji platformy Azure,** a następnie wybierz klaster docelowy. Narzędzie automatycznie rozwiązuje połączenie i zapisuje parametry bezpiecznego połączenia w profilu publikowania.
4. Opcjonalnie: Można edytować profil publikowania, aby określić bezpieczne połączenie klastra.
   
   Ponieważ ręcznie edytujesz plik XML profilu publikowania w celu określenia informacji o certyfikacie, należy zwrócić uwagę na nazwę magazynu certyfikatów, lokalizację magazynu i odcisk palca certyfikatu. Należy podać te wartości dla nazwy sklepu certyfikatu i lokalizacji sklepu. Zobacz [Jak: Pobieranie odcisku palca certyfikatu, aby](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) uzyskać więcej informacji.
   
   Za pomocą *parametrów ClusterConnectionParameters* można określić parametry programu PowerShell używane podczas łączenia się z klastrem sieci szkieletowej usług. Prawidłowe parametry są wszystkie, które są akceptowane przez connect-ServiceFabricCluster polecenia cmdlet. Zobacz [Connect-ServiceFabricCluster listę](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) dostępnych parametrów.
   
   W przypadku publikowania w klastrze zdalnym należy określić odpowiednie parametry dla tego określonego klastra. Oto przykład łączenia się z klastrem niezabezpieczonego:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Oto przykład łączenia się z bezpiecznym klastrem opartym na certyfikatach x509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Edytuj inne niezbędne ustawienia, takie jak parametry uaktualnienia i lokalizacja pliku parametru aplikacji, a następnie opublikuj aplikację z okna dialogowego **Publikowanie aplikacji sieci szkieletowej usługi** w programie Visual Studio.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uzyskiwania dostępu do klastrów sieci szkieletowej usług, zobacz [Wizualizacja klastra przy użyciu Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
