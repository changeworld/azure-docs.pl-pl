---
title: Wyświetlanie i modyfikowanie nazw hostów | Dokumentacja firmy Microsoft
description: Jak wyświetlić i zmienić nazwy hostów dla maszyn wirtualnych platformy Azure, sieci web i role procesów roboczych do rozpoznawania nazw
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3fdb0f566789382a1606b19e4fac179f9ecf40cd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122961"
---
# <a name="viewing-and-modifying-hostnames"></a>Wyświetlanie i modyfikowanie nazw hostów
Aby umożliwić wystąpień roli przywoływanie według nazwy hosta, należy ustawić wartość dla nazwy hosta w pliku konfiguracji usługi dla każdej roli. Można to zrobić, dodając nazwę wybranego hosta do **vmName** atrybutu **roli** elementu. Wartość **vmName** atrybut jest używany jako podstawa dla nazwy hosta dla wszystkich wystąpień roli. Na przykład jeśli **vmName** jest *webrole* istnieją trzy wystąpienia tej roli, nazwy hosta wystąpień będą *webrole0*, *webrole1*, i *webrole2*. Nie należy określić nazwę hosta dla maszyn wirtualnych w pliku konfiguracji, ponieważ nazwa hosta dla maszyny wirtualnej jest wypełniana na podstawie nazwy maszyny wirtualnej. Aby uzyskać więcej informacji na temat konfigurowania usługi Microsoft Azure, zobacz [schematu konfiguracji usługi platformy Azure (cscfg pliku)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Wyświetlanie nazw hostów
Możesz wyświetlić nazwy hostów maszyn wirtualnych i wystąpień roli w usłudze w chmurze przy użyciu dowolnej z poniższych narzędzi.

### <a name="service-configuration-file"></a>Plik konfiguracji usługi
Możesz pobrać plik konfiguracji usługi dla wdrożonej usługi z **Konfiguruj** bloku usługi w witrynie Azure portal. Następnie można wyszukać **vmName** atrybutu dla **nazwy roli** element, aby wyświetlić nazwę hosta. Należy pamiętać, że ta nazwa hosta jest używana jako podstawa dla nazwy hosta dla wszystkich wystąpień roli. Na przykład jeśli **vmName** jest *webrole* istnieją trzy wystąpienia tej roli, nazwy hosta wystąpień będą *webrole0*, *webrole1*, i *webrole2*.

### <a name="remote-desktop"></a>Pulpit zdalny
Po włączeniu usług pulpitu zdalnego (Windows), komunikacji zdalnej programu Windows PowerShell (Windows) lub połączenia SSH (z systemem Linux i Windows) do maszyn wirtualnych lub wystąpień roli, można wyświetlić nazwę hosta z aktywnego połączenia usług pulpitu zdalnego na różne sposoby:

* Wpisz nazwę hosta, w wierszu polecenia lub terminalu SSH.
* Typ ipconfig/wszystko w wierszu polecenia monit (tylko Windows).
* Wyświetl nazwę komputera w ustawieniach systemowych (tylko Windows).

### <a name="azure-service-management-rest-api"></a>Interfejs API REST zarządzania usługi platformy Azure
Od klienta REST wykonaj następujące instrukcje:

1. Upewnij się, że certyfikat klienta, aby nawiązać połączenie z witryny Azure portal. Aby uzyskać certyfikat klienta, wykonaj kroki przedstawione w [jak: Pobieranie i importowanie publikowania, ustawienia i informacje o subskrypcji](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ustaw wpis nagłówka o nazwie x-ms-version o wartości 2013-11-01.
3. Wyślij żądanie w następującym formacie: https:\//management.core.windows.net/\<identyfikator subscrition\>/services/hostedservices/\<nazwa usługi\>? szczegóły osadzania = true
4. Wyszukaj **HostName** elementu dla każdego **RoleInstance** elementu.

> [!WARNING]
> Możesz również wyświetlić sufiks domeny wewnętrznej dla usługi w chmurze z odpowiedzi wywołania REST, sprawdzając **InternalDnsSuffix** elementu, lub przez uruchomienie polecenia ipconfig/wszystko w wierszu polecenia w sesji pulpitu zdalnego (Windows) lub przez Uruchamianie cat /etc/resolv.conf z terminala SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modyfikowanie nazwy hosta
Można zmodyfikować nazwę hosta dla maszyny wirtualnej lub wystąpienia roli, przekazując plik konfiguracji zmodyfikowaną usługę lub zmiana nazwy komputera z sesji pulpitu zdalnego.

## <a name="next-steps"></a>Kolejne kroki
[Rozpoznawanie nazw (domen DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schemat konfiguracji usługi platformy Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schemat konfiguracji sieci wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Określanie ustawień usługi DNS za pomocą plików konfiguracji sieci](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

