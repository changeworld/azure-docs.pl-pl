---
title: Wdrażanie programu IBM DB2 pureScale na platformie Azure
description: Dowiedz się, jak wdrożyć architekturę przykładu ostatnio używany podczas migracji przedsiębiorstwa z jego środowiska programu IBM DB2 uruchomionego na z/OS do pureScale IBM DB2 na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: fba6b5308b380b374611c09747302dbf8305dd9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60716051"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Wdrażanie programu IBM DB2 pureScale na platformie Azure

W tym artykule opisano sposób wdrażania [przykład architektury](ibm-db2-purescale-azure.md) używany Klient firmowy niedawno migracji z jego środowiska programu IBM DB2 uruchomionego na z/OS do pureScale IBM DB2 na platformie Azure.

Aby wykonać kroki używane do migracji, zobacz skryptów instalacji w [DB2onAzure](https://aka.ms/db2onazure) repozytorium w witrynie GitHub. Skrypty te są oparte na architektury dla obciążeń przetwarzania (OLTP) typowe, średnich transakcji online.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć tę architekturę, Pobierz, a następnie uruchom skrypt deploy.sh w [DB2onAzure](https://aka.ms/db2onazure) repozytorium w witrynie GitHub.

Repozytorium zawiera także skrypty do konfigurowania na pulpicie nawigacyjnym Grafana. Pulpit nawigacyjny służy do wykonywania zapytań Prometheus, typu open-source monitorowania i zgłaszania alertów system, dołączone do bazy danych DB2.

> [!NOTE]
> Skrypt deploy.sh na kliencie prywatnych kluczy SSH i przekazuje je do szablonu wdrożenia przy użyciu protokołu HTTPS. Ze względów bezpieczeństwa zaleca się używanie [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) do przechowywania wpisów tajnych, kluczy i haseł.

## <a name="how-the-deployment-script-works"></a>Sposób działania skryptu wdrożenia

Skrypt deploy.sh tworzy i konfiguruje zasoby platformy Azure dla tej architektury. Skrypt wyświetli monit o podanie subskrypcji platformy Azure i maszynami wirtualnymi używanymi w środowisku docelowym, a następnie wykonuje następujące operacje:

-   Konfiguruje grupę zasobów, sieci wirtualnej i podsieci na platformie Azure w celu instalacji

-   Konfiguruje sieciowych grup zabezpieczeń i ustawieniami SSH dla środowiska

-   Konfiguruje kart sieciowych w GlusterFS i na maszynach wirtualnych pureScale bazy danych DB2

-   Tworzy maszyny wirtualne magazynu GlusterFS

-   Tworzy maszynę wirtualną serwera przesiadkowego

-   Tworzy maszyny wirtualne pureScale bazy danych DB2

-   Tworzy maszynę wirtualną monitora polecenia ping z pureScale tej bazy danych DB2

-   Tworzy maszynę wirtualną Windows na potrzeby testowania, ale niczego nie instaluje na nim

Następnie skrypty wdrażania skonfiguruj iSCSI wirtualna sieć SAN (vSAN) jako magazynu udostępnionego na platformie Azure. W tym przykładzie iSCSI łączy się z GlusterFS. To rozwiązanie oferuje również możliwość zainstalowania obiektów docelowych iSCSI jako jeden węzeł Windows. iSCSI udostępnia interfejs magazynu udostępnionego bloku za pośrednictwem protokołu TCP/IP, która umożliwia korzystanie z interfejsu urządzenia do połączenia z magazynem udostępnionym procedury instalacji pureScale bazy danych DB2. Podstawy GlusterFS temacie [architektury: Typy woluminów](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) temat w witrynie Docs Gluster.

Skrypty wdrażania, uruchom następujące ogólne kroki:

1.  GlusterFS umożliwia konfigurowanie klastra magazynu udostępnionego na platformie Azure. Ten krok obejmuje co najmniej dwóch węzłów systemu Linux. Ustawienia szczegółowe informacje, zobacz [Konfigurowanie Red Hat Gluster Storage w systemie Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) w systemie Red Hat Gluster dokumentacji.

2.  Konfigurowanie bezpośredniego interfejsu iSCSI na serwerach docelowych z systemem Linux dla GlusterFS. Szczegóły instalacji można znaleźć [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) w podręczniku administratora GlusterFS.

3.  Konfigurowanie inicjatora iSCSI na maszynach wirtualnych systemu Linux. Inicjator będzie dostęp do klastra GlusterFS przy użyciu obiektu docelowego iSCSI. Szczegóły instalacji można znaleźć [jak konfigurować wiadomość iSCSI docelowego i inicjatora w systemie Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) w dokumentacji RootUsers.

4.  Zainstaluj GlusterFS jako Warstwa przechowywania dla interfejsu iSCSI.

Po skrypty utworzyć urządzenie iSCSI, ostatnim krokiem jest zainstalowanie pureScale bazy danych DB2. Jako część instalacji pureScale bazy danych DB2 [IBM Spectrum skalowania](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (wcześniej znane jako GPFS) są kompilowane i zainstalować w klastrze GlusterFS. Ta klastrowanego systemu plików umożliwia pureScale bazy danych DB2 udostępniać dane między maszynami wirtualnymi, systemem aparatu pureScale bazy danych DB2. Aby uzyskać więcej informacji, zobacz [IBM Spectrum skalowania](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentacji w witrynie internetowej firmy IBM.

## <a name="db2-purescale-response-file"></a>Plik odpowiedzi pureScale bazy danych DB2

Repozytorium GitHub zawiera DB2server.rsp, plik odpowiedzi (rsp), który umożliwia generowanie zautomatyzowanych skryptów instalacji pureScale bazy danych DB2. Poniższej tabeli wymieniono opcje pureScale bazy danych DB2, które korzysta z pliku odpowiedzi dla instalacji. Można dostosować plik odpowiedzi, zgodnie z potrzebami w danym środowisku.

> [!NOTE]
> Przykładowy plik odpowiedzi, DB2server.rsp, znajduje się w [DB2onAzure](https://aka.ms/db2onazure) repozytorium w witrynie GitHub. Jeśli używasz tego pliku, należy zmodyfikować przed może pracować w swoim środowisku.

| Nazwa ekranowa               | Pole                                        | Wartość                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Witaj                   |                                              | Nowej instalacji                                                                                           |
| Wybierz produkt          |                                              | Bazy danych DB2 wersja 11.1.3.3. Wersje serwera z pureScale bazy danych DB2                                              |
| Konfigurowanie             | Katalog                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Wybieranie typu instalacji                 | Typowe                                                                                               |
|                           | Wyrażam zgodę na warunki firmy IBM                     | Zaznaczone                                                                                               |
| Właściciel wystąpienia            | Istniejące wystąpienie dla użytkownika, nazwa użytkownika        | DB2sdin1                                                                                              |
| Ogrodzone użytkownika               | Istniejącego użytkownika, nazwa użytkownika                     | DB2sdfe1                                                                                              |
| System plików klastra       | Udostępnione ścieżki urządzenia partycji dysku            | /dev/DM-2                                                                                             |
|                           | Punkt instalacji                                  | /DB2sd\_1804a                                                                                         |
|                           | Udostępniony dysk danych                         | /dev/DM-1                                                                                             |
|                           | Punkt instalacji (dane)                           | /DB2fs/datafs1                                                                                        |
|                           | Udostępniony dysk dla dzienników                          | /dev/DM-0                                                                                             |
|                           | Punkt instalacji (dziennik)                            | /DB2fs/logfs1                                                                                         |
|                           | Bazy danych DB2 Tiebreaker usług klastrowania. Ścieżka urządzenia | /dev/dm-3                                                                                             |
| Lista hostów                 | D1 [eth1] d2 [eth1] cf1 [eth1] cf2 [eth1] |                                                                                                       |
|                           | Preferowane podstawowej usługi CF                         | cf1                                                                                                   |
|                           | Preferowane pomocnicze usługi CF                       | cf2                                                                                                   |
| Plik odpowiedzi i podsumowanie | Pierwsza opcja                                 | Instalowanie serwera bazy danych DB2 w wersji z funkcją pureScale IBM DB2 i Zapisz moje ustawienia w pliku odpowiedzi |
|                           | Nazwa pliku odpowiedzi                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Uwagi dotyczące tego wdrożenia

- Wartości /dev-dm0, /dev-dm1, /dev-dm2 i /dev-dm3 można zmienić po ponownym uruchomieniu maszyny wirtualnej, jeśli Instalator ma miejsce (d0 w zautomatyzowanego skryptu). Aby znaleźć odpowiednie wartości, należy wydać następujące polecenie przed ukończeniem pliku odpowiedzi na serwerze, na której zostanie uruchomiony Instalator:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Skrypty instalacji na użytek aliasy dyski iSCSI, aby można je znaleźć łatwo rzeczywistymi nazwami.

- Po uruchomieniu skryptu instalacyjnego na d0, **/dev/dm -\***  wartości mogą się różnić w d1, cf0 i cf1. Różnica w wartości nie ma wpływu na instalacji pureScale bazy danych DB2.

## <a name="troubleshooting-and-known-issues"></a>Znane problemy i rozwiązywanie problemów

Repozytorium GitHub zawiera wiedzy, która utrzymuje autorów. Wyświetla listę potencjalnych problemów, które mogą mieć i rozwiązania, które można wypróbować. Na przykład, znanych problemów może się zdarzyć, gdy:

-   Próbujesz osiągnąć adres IP bramy.

-   W przypadku kompilowania ogólnych licencji publicznej (GPL).

-   Uzgadnianie zabezpieczeń między hostami kończy się niepowodzeniem.

-   Instalator programu DB2 wykrywa istniejący system plików.

-   Ręczne instalowanie IBM Spectrum skali.

-   W przypadku instalowania bazy danych DB2 pureScale podczas IBM Spectrum skala jest już utworzony.

-   Usuwasz pureScale bazy danych DB2 i IBM Spectrum skali.

Aby uzyskać więcej informacji na temat tych i innych znanych problemów, zobacz plik kb.md w [DB2onAzure](https://aka.ms/DB2onAzure) repozytorium.

## <a name="next-steps"></a>Kolejne kroki

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Tworzenie użytkowników wymagane dla bazy danych DB2 pureScale instalacji funkcji](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt — Utwórz wystąpienie, polecenie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Bazy danych DB2 pureScale klastrów rozwiązanie do przetwarzania danych](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Alliance modernizacji platformy: IBM DB2 na platformie Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Wirtualne centrum danych usługi Azure Lift- and -Shift przewodnik](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
