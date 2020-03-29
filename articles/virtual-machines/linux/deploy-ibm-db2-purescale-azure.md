---
title: Wdrażanie ibm db2 pureScale na platformie Azure
description: Dowiedz się, jak wdrożyć przykładową architekturę używaną ostatnio do migracji przedsiębiorstwa ze środowiska IBM DB2 działającego na z/OS do ibm DB2 pureScale na platformie Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968896"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Wdrażanie ibm db2 pureScale na platformie Azure

W tym artykule opisano sposób wdrażania [przykładowej architektury,](ibm-db2-purescale-azure.md) która została ostatnio użyta przez klienta korporacyjnego do migracji ze środowiska IBM DB2 działającego w systemie z/OS do ibm DB2 pureScale na platformie Azure.

Aby wykonać kroki używane do migracji, zobacz skrypty instalacji w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w usłudze GitHub. Skrypty te są oparte na architekturze dla typowego, średniej wielkości obciążenia przetwarzania transakcji online (OLTP).

## <a name="get-started"></a>Wprowadzenie

Aby wdrożyć tę architekturę, pobierz i uruchom skrypt deploy.sh znaleziony w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w usłudze GitHub.

Repozytorium posiada również skrypty do konfigurowania pulpitu nawigacyjnego Grafana. Za pomocą pulpitu nawigacyjnego można wysyłać zapytania do Prometeusza, systemu monitorowania i ostrzegania typu open source dołączonego do DB2.

> [!NOTE]
> Skrypt deploy.sh na kliencie tworzy prywatne klucze SSH i przekazuje je do szablonu wdrożenia za pośrednictwem protokołu HTTPS. Aby zwiększyć bezpieczeństwo, zalecamy używanie [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) do przechowywania wpisów tajnych, kluczy i haseł.

## <a name="how-the-deployment-script-works"></a>Jak działa skrypt wdrażania

Skrypt deploy.sh tworzy i konfiguruje zasoby platformy Azure dla tej architektury. Skrypt monituje o subskrypcję platformy Azure i maszyny wirtualne używane w środowisku docelowym, a następnie wykonuje następujące operacje:

-   Konfiguruje grupę zasobów, sieć wirtualną i podsieci na platformie Azure dla instalacji.

-   Konfiguruje sieciowe grupy zabezpieczeń i SSH dla środowiska.

-   Konfiguruje wiele kart sieciowych zarówno w udostępnionym magazynie, jak i na maszynach wirtualnych db2 pureScale.

-   Tworzy udostępnione maszyny wirtualne magazynu. Jeśli używasz bezpośredniego miejsca do magazynowania lub innego rozwiązania magazynu, zobacz [Omówienie bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Tworzy maszynę wirtualną jumpbox.

-   Tworzy maszyny wirtualne DB2 pureScale.

-   Tworzy maszynę wirtualną świadka, która DB2 pureScale pings. Pomiń tę część wdrożenia, jeśli twoja wersja Db2 pureScale nie wymaga monitora.

-   Tworzy maszynę wirtualną systemu Windows do użycia do testowania, ale nie instaluje na niej niczego.

Następnie skrypty wdrażania skonfigurować sieci wirtualnego obszaru magazynu iSCSI (vSAN) dla udostępnionego magazynu na platformie Azure. W tym przykładzie iSCSI łączy się z klastrem magazynu udostępnionego. W oryginalnym rozwiązaniu dla klienta zastosowano GlusterFS. Jednak IBM nie obsługuje już tego podejścia. Aby utrzymać wsparcie ibm, należy użyć obsługiwanego systemu plików zgodnego z interfejsem iSCSI. Microsoft oferuje bezpośrednie miejsca do magazynowania (S2D) jako opcja.

To rozwiązanie umożliwia również zainstalowanie obiektów docelowych iSCSI jako pojedynczego węzła systemu Windows. iSCSI udostępnia interfejs magazynu bloków udostępnionych za pomocą protokołu TCP/IP, który umożliwia procedurze konfiguracji db2 pureScale używanie interfejsu urządzenia do łączenia się z magazynem udostępnionym.

Skrypty wdrażania uruchamiają następujące ogólne kroki:

1.  Konfigurowanie udostępnionego klastra magazynu na platformie Azure. Ten krok obejmuje co najmniej dwa węzły systemu Linux.

2.  Skonfiguruj interfejs iSCSI Direct na docelowych serwerach systemu Linux dla udostępnionego klastra magazynu.

3.  Skonfiguruj inicjatora iSCSI na maszynach wirtualnych systemu Linux. Inicjator będzie uzyskiwać dostęp do udostępnionego klastra magazynu przy użyciu obiektu docelowego iSCSI. Aby uzyskać szczegółowe informacje na temat konfiguracji, zobacz [Jak skonfigurować cel i inicjatora iSCSI w systemie Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) w dokumentacji RootUsers.

4.  Zainstaluj udostępnioną warstwę magazynu dla interfejsu iSCSI.

Po utworzeniu urządzenia iSCSI przez skrypty ostatnim krokiem jest zainstalowanie programu DB2 pureScale. W ramach konfiguracji DB2 pureScale, [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (wcześniej znany jako GPFS) jest kompilowany i instalowany w klastrze GlusterFS. Ten klastrowany system plików umożliwia DB2 pureScale udostępnianie danych między maszynami wirtualnymi, które uruchamiają aparat DB2 pureScale. Więcej informacji można znaleźć w dokumentacji [ibm spectrum scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) w witrynie IBM w sieci Web.

## <a name="db2-purescale-response-file"></a>Plik odpowiedzi pureScale DB2

Repozytorium GitHub zawiera plik DB2server.rsp, plik odpowiedzi (.rsp), który umożliwia wygenerowanie automatycznego skryptu dla instalacji DB2 pureScale. W poniższej tabeli wymieniono opcje DB2 pureScale używane do konfiguracji przez plik odpowiedzi. Plik odpowiedzi można dostosować w zależności od potrzeb w swoim środowisku.

> [!NOTE]
> Przykładowy plik odpowiedzi, DB2server.rsp, znajduje się w repozytorium [DB2onAzure](https://aka.ms/db2onazure) w usłudze GitHub. Jeśli używasz tego pliku, należy go edytować, zanim będzie mógł działać w twoim środowisku.

| Nazwa ekranu               | Pole                                        | Wartość                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Powitanie                   |                                              | Nowa instalacja                                                                                           |
| Wybierz produkt          |                                              | DB2 wersja 11.1.3.3. Wersje serwerowe z DB2 pureScale                                              |
| Konfigurowanie             | Katalog                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Wybierz typ instalacji                 | Typowe                                                                                               |
|                           | Zgadzam się na warunki IBM                     | Zaznaczone                                                                                               |
| Właściciel wystąpienia            | Istniejący użytkownik na przykład, nazwa użytkownika        | DB2sdin1                                                                                              |
| Użytkownik ogrodzony               | Istniejący użytkownik, nazwa użytkownika                     | DB2sdfe1                                                                                              |
| System plików klastra       | Ścieżka urządzenia partycji dysku udostępnionego            | /dev/dm-2                                                                                             |
|                           | Punkt montażu                                  | /DB2sd\_1804a                                                                                         |
|                           | Udostępniony dysk danych                         | /dev/dm-1                                                                                             |
|                           | Punkt instalacji (dane)                           | /DB2fs/datafs1                                                                                        |
|                           | Udostępniony dysk do dziennika                          | /dev/dm-0                                                                                             |
|                           | Punkt instalacji (dziennik)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services Tiebreaker. Ścieżka urządzenia | /dev/dm-3                                                                                             |
| Lista hostów                 | d1 [eth1], d2 [eth1], cf1 [eth1], por2 [eth1] |                                                                                                       |
|                           | Preferowany podstawowy mukowiscydozja                         | por1                                                                                                   |
|                           | Preferowany wtórny mukowiscydozję                       | por2                                                                                                   |
| Plik odpowiedzi i podsumowanie | pierwsza opcja                                 | Zainstaluj DB2 Server Edition z funkcją IBM DB2 pureScale i zapisz moje ustawienia w pliku odpowiedzi |
|                           | Nazwa pliku odpowiedzi                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Uwagi dotyczące tego wdrożenia

- Wartości dla /dev-dm0, /dev-dm1, /dev-dm2 i /dev-dm3 mogą ulec zmianie po ponownym uruchomieniu na maszynie wirtualnej, na której odbywa się konfiguracja (d0 w skrypcie automatycznym). Aby znaleźć odpowiednie wartości, można wydać następujące polecenie przed zakończeniem pliku odpowiedzi na serwerze, na którym zostanie uruchomiony instalator:

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

- Skrypty instalacyjne używają aliasów dla dysków iSCSI, dzięki czemu można łatwo znaleźć rzeczywiste nazwy.

- Gdy skrypt konfiguracji jest uruchamiany na d0, **/dev/dm-\* ** wartości mogą być różne na d1, cf0 i cf1. Różnica w wartościach nie wpływa na ustawienia pureScale DB2.

## <a name="troubleshooting-and-known-issues"></a>Znane problemy i rozwiązywanie problemów

Repozytorium GitHub zawiera bazę wiedzy, którą utrzymują autorzy. Zawiera listę potencjalnych problemów, które możesz mieć, i rozwiązania, które możesz wypróbować. Na przykład znane problemy mogą się zdarzyć, gdy:

-   Próbujesz dotrzeć do adresu IP bramy.

-   Kompilujesz Ogólną Licencję Publiczną (GPL).

-   Uzgadnianie zabezpieczeń między hostami kończy się niepowodzeniem.

-   Instalator DB2 wykrywa istniejący system plików.

-   Instalujesz program IBM Spectrum Scale ręcznie.

-   Instalujesz DB2 pureScale, gdy ibm spectrum scale jest już utworzony.

-   Usuwasz DB2 pureScale i IBM Spectrum Scale.

Aby uzyskać więcej informacji na temat tych i innych znanych problemów, zobacz plik kb.md w repozytorium [DB2onAzure.](https://aka.ms/DB2onAzure)

## <a name="next-steps"></a>Następne kroki

-   [Tworzenie wymaganych użytkowników dla instalacji funkcji pureScale DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt — polecenie Utwórz wystąpienie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Rozwiązanie danych klastrów o czystej skali DB2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Przewodnik po wirtualnym centrum danych azure i przeniesieniu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
