---
title: Skonfigurować bezpieczne połączenia klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bezpieczne połączenia, które są obsługiwane przez klaster sieci szkieletowej usług Azure za pomocą programu Visual Studio.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: e2772cc2c59b93c7e523eaa0127dcf4ea0bc589e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208689"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Skonfigurować bezpieczne połączenia z klastrem usługi sieć szkieletowa usług w programie Visual Studio
Dowiedz się, jak bezpieczny dostęp do klastra usługi sieć szkieletowa usług Azure przy użyciu zasad kontroli dostępu skonfigurowana za pomocą programu Visual Studio.

## <a name="cluster-connection-types"></a>Typy połączeń z klastra
Dwa typy połączeń są obsługiwane przez klaster sieci szkieletowej usług Azure: **niezabezpieczonego** połączeń i **x509 opartego na certyfikatach** bezpiecznych połączeń. (Dla klastrów sieci szkieletowej usług hostowanych w infrastrukturze lokalnej, **Windows** i **dSTS** uwierzytelnienia są również obsługiwane.) Należy skonfigurować typ połączenia klastra podczas tworzenia klastra. Po utworzeniu, nie można zmienić typu połączenia.

Narzędzia Visual Studio sieci szkieletowej usług obsługuje wszystkie typy uwierzytelniania do łączenia się z klastrem publikowania. Zobacz [konfigurowania klastra sieci szkieletowej usług w portalu Azure](service-fabric-cluster-creation-via-portal.md) instrukcje dotyczące sposobu konfigurowania bezpiecznej klastra sieci szkieletowej usług.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurowanie połączeń z klastrem w profilów publikowania
Jeśli podczas publikowania projektu sieci szkieletowej usług w programie Visual Studio, użyj **publikowania aplikacji sieci szkieletowej usług** okno dialogowe, aby wybrać klastra usługi sieć szkieletowa usług Azure. W obszarze **punktu końcowego połączenia**, wybierz istniejący klaster, w ramach Twojej subskrypcji.

![** Opublikować usługi sieci szkieletowej aplikacji ** — okno dialogowe służy do konfigurowania połączeń sieci szkieletowej usług.][publishdialog]

**Publikowania aplikacji sieci szkieletowej usług** okno dialogowe automatycznie sprawdza poprawność połączenia klastra. W przypadku wyświetlenia monitu zaloguj się do konta platformy Azure. Jeśli weryfikacja zakończy się pomyślnie, oznacza to, że system ma poprawne certyfikaty zainstalowane na bezpieczne łączenie z klastrem lub klaster jest niebezpieczne. Wystąpiły błędy sprawdzania poprawności może być spowodowane przez problemy z siecią lub nie ma poprawnie skonfigurowany do nawiązania bezpiecznego klastra systemu.

![** Opublikować usługi sieci szkieletowej aplikacji ** — okno dialogowe weryfikuje istniejące, poprawnie skonfigurowany połączenia klastra sieci szkieletowej usług.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Do nawiązania bezpiecznego klastra
1. Upewnij się, że dostępne certyfikaty klienta, które ufa klastra docelowego. Certyfikat jest zwykle udostępniany jako plik wymiany informacji osobistych (pfx). Zobacz [konfigurowania klastra sieci szkieletowej usług w portalu Azure](service-fabric-cluster-creation-via-portal.md) dotyczące sposobu konfigurowania serwera w celu udzielenia dostępu do klienta.
2. Instalowanie zaufanych certyfikatów. Aby to zrobić, kliknij dwukrotnie plik PFX, lub użyj skryptu programu PowerShell Import PfxCertificate do importowania certyfikatów. Zainstaluj certyfikat do **Cert: \LocalMachine\My**. Jest OK, aby zaakceptować wszystkie ustawienia domyślne podczas importowania certyfikatu.
3. Wybierz **publikowania...**  polecenia menu skrótów projektu, aby otworzyć **publikowanie aplikacji platformy Azure** okno dialogowe, a następnie wybierz klaster docelowy. Narzędzie automatycznie rozpoznaje połączenia i parametry bezpiecznego połączenia są zapisywane w profilu publikowania.
4. Opcjonalnie: Można edytować profilu publikowania do określania połączenia bezpiecznego klastra.
   
   Ponieważ ręcznie edytowany plik XML profilu publikowania, aby określić informacje o certyfikacie, zanotuj nazwę magazynu certyfikatów, przechowywać lokalizacji i odcisk palca certyfikatu. Należy podać te wartości magazynu certyfikatów nazwy i lokalizacji magazynu. Zobacz [porady: Pobieranie odcisku palca certyfikatu](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) Aby uzyskać więcej informacji.
   
   Można użyć *ClusterConnectionParameters* parametrów, aby określić parametry programu PowerShell do użycia podczas połączenia z klastrem usługi sieć szkieletowa usług. Prawidłowe parametry to, że są akceptowane przez polecenie cmdlet Connect-ServiceFabricCluster. Zobacz [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) listę dostępnych parametrów.
   
   Jeśli jest publikowany klastra zdalnego, należy określić odpowiednie parametry dla tego konkretnego klastra. Poniżej przedstawiono przykład łączenia się z klastrem niezabezpieczonego:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Oto przykład w celu nawiązania x509 opartego na certyfikatach bezpiecznego klastra:
   
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
5. Edytować inne wymagane ustawienia, takie jak parametry uaktualniania i lokalizacja pliku parametrów aplikacji, a następnie opublikować aplikacji z **publikowania aplikacji sieci szkieletowej usług** okno dialogowe w programie Visual Studio.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat uzyskiwania dostępu do sieci szkieletowej usług klastrów, zobacz [wizualizacja klastra przy użyciu Eksploratora usługi sieć szkieletowa](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
