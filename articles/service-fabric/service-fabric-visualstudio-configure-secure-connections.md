---
title: Konfigurowanie bezpiecznych połączeń z klastrem usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Konfigurowanie bezpiecznych połączeń, które są obsługiwane przez klaster usługi Azure Service Fabric przy użyciu programu Visual Studio.
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
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628300"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurowanie bezpiecznych połączeń z klastrem usługi Service Fabric z poziomu programu Visual Studio
Dowiedz się, jak używać programu Visual Studio bezpiecznego dostępu do klastra usługi Azure Service Fabric przy użyciu zasad kontroli dostępu skonfigurowana.

## <a name="cluster-connection-types"></a>Typy połączeń klastra
Dwa typy połączeń obsługiwanych przez klaster usługi Azure Service Fabric: **— Zabezpieczanie** połączeń i **x509 opartego na certyfikatach** bezpieczne połączenia. (Dla klastrów usługi Service Fabric hostowane lokalnie, **Windows** i **dSTS** uwierzytelnienia są również obsługiwane.) Należy skonfigurować typ połączenia klastra, po utworzeniu klastra. Po jego utworzeniu nie można zmienić typu połączenia.

Narzędzia Visual Studio Service Fabric obsługuje wszystkie typy uwierzytelniania do łączenia się z klastrem publikowania. Zobacz [Konfigurowanie klastra usługi Service Fabric w witrynie Azure portal](service-fabric-cluster-creation-via-portal.md) instrukcje dotyczące sposobu konfigurowania bezpiecznego klastra usługi Service Fabric.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurowanie połączeń z klastrem w Profile publikowania
Jeśli opublikujesz projekt usługi Service Fabric z poziomu programu Visual Studio, użyj **publikowanie aplikacji usługi Service Fabric** okno dialogowe, wybierz klaster usługi Azure Service Fabric. W obszarze **punkt końcowy połączenia**, wybierz istniejący klaster w ramach Twojej subskrypcji.

![** Okno dialogowe publikowania usługi Service Fabric aplikacji ** służy do konfigurowania połączeń usługi Service Fabric.][publishdialog]

**Publikowanie aplikacji usługi Service Fabric** okno dialogowe automatycznie sprawdza poprawność połączenia klastra. Jeśli zostanie wyświetlony monit, zaloguj się do konta platformy Azure. Jeśli weryfikacja zakończy się pomyślnie, oznacza to, że system ma prawidłowe certyfikaty zainstalowane do bezpiecznego łączenia się z klastrem lub klaster jest niebezpieczne. Błędy sprawdzania poprawności może być spowodowany przez problemy z siecią lub nie ma systemu poprawnie skonfigurowanej do połączenia z zabezpieczonym klastrem.

![** Publikowania usługi Service Fabric aplikacji ** okno dialogowe weryfikuje istniejącej poprawnie skonfigurowane połączenie klastra usługi Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Aby nawiązać połączenie z zabezpieczonym klastrem
1. Upewnij się, że dostęp do jednego z certyfikatów klienta, które zaufania w klastrze docelowym. Certyfikat jest zazwyczaj udostępniany jako plik wymiany informacji osobistych (pfx). Zobacz [Konfigurowanie klastra usługi Service Fabric w witrynie Azure portal](service-fabric-cluster-creation-via-portal.md) dotyczące sposobu konfigurowania serwera, aby udzielić dostępu do klienta.
2. Zainstaluj zaufanego certyfikatu. Aby to zrobić, kliknij dwukrotnie plik PFX, lub użyj skryptu programu PowerShell Import PfxCertificate do importowania certyfikatów. Zainstaluj certyfikat na **Cert: \LocalMachine\My**. Jest OK, aby zaakceptować wszystkie ustawienia domyślne podczas importowania certyfikatu.
3. Wybierz **publikowania...**  polecenia menu skrótów projektu, aby otworzyć **publikowanie aplikacji platformy Azure** okno dialogowe, a następnie wybierz klaster docelowy. Narzędzie jest rozpoznawana jako połączenie i automatycznie zapisuje parametry bezpiecznego połączenia w profilu publikowania.
4. Opcjonalnie: Można edytować profilu publikowania do określania połączenia z zabezpieczonym klastrem.
   
   Ponieważ ręcznie edytowany plik XML profilu publikowania, aby określić informacje o certyfikacie, koniecznie Zanotuj nazwę magazynu certyfikatów, przechowywać, lokalizacji i odcisk palca certyfikatu. Należy podać te wartości do magazynu certyfikatów nazwy i lokalizacji magazynu. Zobacz [Instrukcje: Pobieranie odcisku palca certyfikatu](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) Aby uzyskać więcej informacji.
   
   Możesz użyć *ClusterConnectionParameters* parametry, aby określić parametry programu PowerShell do użycia podczas łączenia się z klastrem usługi Service Fabric. Prawidłowe parametry to wszystkie, które są akceptowane przez polecenia cmdlet Connect-ServiceFabricCluster. Zobacz [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) listę dostępnych parametrów.
   
   Jeśli publikujesz do zdalnego klastra, należy określić odpowiednie parametry dla tego określonego klastra. Oto przykład łączenia się z niezabezpieczonym klastrem:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Oto przykład dotyczący nawiązywania połączenia z x509 opartego na certyfikatach zabezpieczonego klastra:
   
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
5. Edytować inne wymagane ustawienia, takie jak parametry uaktualniania i lokalizacja pliku parametrów aplikacji, a następnie opublikować aplikacji z **publikowanie aplikacji usługi Service Fabric** okno dialogowe w programie Visual Studio.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat uzyskiwania dostępu do klastrów usługi Service Fabric, zobacz [wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
