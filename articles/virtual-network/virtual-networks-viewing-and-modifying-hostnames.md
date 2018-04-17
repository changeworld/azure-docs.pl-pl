---
title: Wyświetlanie i modyfikowanie nazwy hostów | Dokumentacja firmy Microsoft
description: Jak przeglądać i zmieniać nazwy hostów maszyn wirtualnych platformy Azure, sieci web i proces roboczy do rozpoznawania nazw
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: genli
ms.openlocfilehash: 6fe3522c1b3e2f5a07de3d12875ae47a830873d3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="viewing-and-modifying-hostnames"></a>Wyświetlanie i modyfikowanie nazwy hostów
Aby zezwolić wystąpienia roli przywoływanie według nazwy hosta, należy ustawić wartość nazwy hosta w pliku konfiguracji usługi dla każdej roli. Można to zrobić przez dodanie nazwy wybranego hosta, aby **vmName** atrybutu **roli** elementu. Wartość **vmName** atrybutu jest używana jako podstawa dla każdego wystąpienia roli nazwy hosta. Na przykład jeśli **vmName** jest *sieć Web* istnieją trzy wystąpień tej roli, nazwy hosta wystąpień będą *webrole0*, *webrole1*, i *webrole2*. Nie trzeba określić nazwę hosta dla maszyn wirtualnych w pliku konfiguracji, ponieważ nazwa hosta dla maszyny wirtualnej jest wypełniana na podstawie nazwy maszyny wirtualnej. Aby uzyskać więcej informacji o konfigurowaniu usługi Microsoft Azure, zobacz [schemat konfiguracji usługi Azure (cscfg plików)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Wyświetlanie nazwy hostów
Można wyświetlić nazwy hostów maszyn wirtualnych i wystąpień roli w usłudze w chmurze przy użyciu dowolnej z poniższych narzędzi.

### <a name="azure-portal"></a>Azure Portal
Można użyć [portalu Azure](http://portal.azure.com) Aby wyświetlić nazwy hostów dla maszyn wirtualnych w bloku omówienie maszyny wirtualnej. Należy pamiętać, że blok zawiera wartość dla **nazwa** i **nazwy hosta**. Chociaż początkowo są takie same, zmiana nazwy hosta nie zmieni nazwę maszyny wirtualnej lub wystąpienia roli.

Można również wyświetlać wystąpień roli w portalu Azure, ale jeśli lista wystąpień w usłudze w chmurze, nazwy hosta nie jest wyświetlana. Zobaczysz nazwy dla każdego wystąpienia, ale ta nazwa reprezentuje nazwę hosta.

### <a name="service-configuration-file"></a>Plik konfiguracji usługi
Możesz pobrać plik konfiguracji usługi dla wdrożonej usługi z **Konfiguruj** bloku usługi w portalu Azure. Następnie można wyszukać **vmName** atrybutu dla **nazwy roli** element, aby zobaczyć nazwę hosta. Należy pamiętać, że ta nazwa hosta jest używana jako podstawa dla każdego wystąpienia roli nazwy hosta. Na przykład jeśli **vmName** jest *sieć Web* istnieją trzy wystąpień tej roli, nazwy hosta wystąpień będą *webrole0*, *webrole1*, i *webrole2*.

### <a name="remote-desktop"></a>Pulpit zdalny
Po włączeniu pulpitu zdalnego (system Windows), komunikacji zdalnej programu Windows PowerShell (Windows) lub połączeń SSH (Linux i Windows) do maszyny wirtualnej lub wystąpień roli można wyświetlić nazwy hosta z aktywnego połączenia pulpitu zdalnego na różne sposoby:

* W wierszu polecenia lub SSH terminal, wpisz nazwę hosta.
* Wpisz polecenie ipconfig/wszystkich w wierszu polecenia monit (tylko system Windows).
* Wyświetl nazwę komputera w ustawieniach systemu (tylko system Windows).

### <a name="azure-service-management-rest-api"></a>Usługa Azure Service Management REST API
W kliencie REST wykonaj następujące instrukcje:

1. Upewnij się, że certyfikat klienta do nawiązania połączenia z portalu Azure. Aby uzyskać certyfikat klienta, wykonaj kroki przedstawione w [porady: pobieranie i importowanie ustawień publikowania i informacji o subskrypcji](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ustaw wpis nagłówka o nazwie x-ms-version o wartości 2013-11-01.
3. Wyślij żądanie w następującym formacie: https://management.core.windows.net/ \<identyfikator subscrition\>/services/hostedservices/\<nazwa usługi\>? osadzić szczegółów = true
4. Wyszukaj **HostName** elementu dla każdego **RoleInstance** elementu.

> [!WARNING]
> Możesz również wyświetlić sufiks domeny wewnętrznej dla usługi w chmurze z odpowiedzi wywołań REST, sprawdzając **InternalDnsSuffix** elementu, lub przez uruchomienie polecenia ipconfig/z wiersza polecenia w sesji pulpitu zdalnego (Windows) lub przez polecenia cat /etc/resolv.conf SSH terminali (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modyfikowanie nazwy hosta
Można zmodyfikować nazwę hosta dla maszyny wirtualnej lub wystąpienia roli, przekazując plik konfiguracji usługi zmodyfikowanych lub zmiana nazwy komputera z sesji pulpitu zdalnego.

## <a name="next-steps"></a>Kolejne kroki
[Rozpoznawanie nazw (domen DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schemat konfiguracji usługi Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schemat konfiguracji sieci wirtualnej platformy Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Określ ustawienia DNS za pomocą plików konfiguracji sieci](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

