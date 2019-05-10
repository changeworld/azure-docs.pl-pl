---
title: Wdrażanie platformy OpenShift Container Platform Samozarządzanej oferty w portalu Marketplace na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie platformy OpenShift Container Platform Samozarządzanej oferty w portalu Marketplace na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233086"
---
# <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych

Przed rozpoczęciem korzystania z oferty w portalu Marketplace, aby wdrożyć klaster z możliwością samodzielnego zarządzania OpenShift Container Platform na platformie Azure, musi być skonfigurowany z kilkoma wymaganiami wstępnymi.  Odczyt [wymagania wstępne platformy OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) artykuł, aby uzyskać instrukcje tworzenia ssh key (bez hasła), usługi Azure key vault, klucza tajnego usługi key vault i nazwy głównej usługi.

 
## <a name="deploy-using-the-marketplace-offer"></a>Wdrażanie przy użyciu oferty w portalu Marketplace

Najprostszym sposobem, aby wdrożyć klaster z możliwością samodzielnego zarządzania OpenShift Container Platform na platformie Azure jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ta opcja jest najprostszym, ale również ma ona ograniczone możliwości dostosowywania. Oferty w portalu Marketplace wdraża OpenShift Container Platform 3.11.82 i zawiera następujące opcje konfiguracji:

- **Węzły główne**: Wzorzec węzłów trzech (3) z możliwością konfiguracji wystąpieniem typu.
- **Infra węzłów**: Trzy (3) Infra węzłów za pomocą konfigurowalnych wystąpienie typu.
- **Węzły**: Liczba węzłów (od 1 do 9) i typu wystąpienia są konfigurowane.
- **Typ dysku**: Dyski zarządzane są używane.
- **Sieć**: Obsługa nowych lub istniejących sieci i niestandardowy zakres CIDR.
- **CNS**: Można włączyć CNS.
- **Metryki**: Można włączyć hawkular metryki.
- **Rejestrowanie**: Można włączyć rejestrowanie EFK.
- **Dostawca usług w chmurze Azure**: Domyślnie włączone, można wyłączyć.

W lewym górnym rogu witryny Azure portal, kliknij przycisk **Utwórz zasób**wprowadź openshift container platform, w polu wyszukiwania i naciśnij klawisz Enter.

   ![Nowe wyszukiwanie zasobów](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Zostanie otwarta strona wyników, za pomocą **Red Hat OpenShift Container Platform Self-Managed** na liście. 

   ![Nowy wynik wyszukiwania zasobów](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kliknij przycisk oferty, aby wyświetlić szczegóły oferty. Aby wdrożyć tę ofertę, kliknij **Utwórz**. Pojawi się interfejs użytkownika, aby wprowadzić niezbędne parametry. Pierwszy ekran **podstawy** bloku.

   ![Oferta tytuł strony](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Podstawy**

Aby uzyskać pomoc dotyczącą dowolnego z parametrów wejściowych, umieść kursor nad ***i*** obok nazwy parametru.

Wprowadź wartości dla parametrów wejściowych, a następnie kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Nazwa użytkownika administratora maszyny Wirtualnej | Użytkownika administracyjnego, który ma zostać utworzony na wszystkich wystąpieniach maszyn wirtualnych |
| SSH klucz publiczny dla użytkownika administratora | Klucz publiczny SSH użyte do zalogowania się do maszyny Wirtualnej — nie mogą zawierać hasła |
| Subskrypcja | Wdrażanie klastra w subskrypcji platformy Azure |
| Grupa zasobów | Utwórz nową grupę zasobów lub wybierz istniejący pustą grupę zasobów dla zasobów klastra |
| Lokalizacja | Region platformy Azure, aby wdrożyć klaster w |

   ![Blok podstawowe oferty](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Ustawienia infrastruktury**

Wprowadź wartości dla parametrów wejściowych, a następnie kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Prefiks nazwy klastra OCP | Klaster prefiks używany do konfigurowania nazw hostów dla wszystkich węzłów. Od 1 do 20 znaków |
| Rozmiar węzła głównego | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar** do wybrania innego rozmiaru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia robocze |
| Rozmiar węzła infrastruktury | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar** do wybrania innego rozmiaru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia robocze |
| Liczba węzłów aplikacji | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar** do wybrania innego rozmiaru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia robocze |
| Rozmiar węzła aplikacji | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar** do wybrania innego rozmiaru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia robocze |
| Rozmiar Host bastionu | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar** do wybrania innego rozmiaru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia robocze |
| Nowej lub istniejącej sieci wirtualnej | Utwórz nową sieć wirtualną (ustawienie domyślne) lub użyj istniejącej sieci wirtualnej |
| Wybierz ustawienia CIDR domyślne lub Dostosuj zakres adresów IP (CIDR) | Zaakceptuj domyślny zakresy CIDR, lub wybierz **niestandardowy zakres adresów IP** i wprowadź informacje o niestandardowym CIDR.  Domyślne ustawienia spowoduje to utworzenie sieci wirtualnej CIDR 10.0.0.0/14, główny podsieć o 10.1.0.0/16 infra podsieć o 10.2.0.0/16 i zasobów obliczeniowych i cns podsieć o 10.3.0.0/16 |
| Nazwa grupa zasobów magazynu kluczy | Nazwa grupy zasobów, który zawiera usługi Key Vault |
| Nazwa usługi Key Vault | Nazwa magazynu kluczy, który zawiera klucz tajny przy użyciu protokołu ssh klucza prywatnego.  Tylko znaki alfanumeryczne i łączniki są dozwolone, a także należeć do zakresu od 3 do 24 znaków |
| Nazwa wpisu tajnego | Nazwa klucza tajnego, który zawiera ssh klucza prywatnego.  Dozwolone są tylko znaki alfanumeryczne i łączniki |

   ![Oferta bloku infrastruktura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Zmiana rozmiaru**

Aby wybrać inny rozmiar maszyny Wirtualnej, kliknij przycisk ***Zmień rozmiar***.  Zostanie otwarte okno wyboru maszyny Wirtualnej.  Wybierz rozmiar maszyny Wirtualnej, a następnie kliknij przycisk **wybierz**.

   ![Wybierz rozmiar maszyny Wirtualnej](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Istniejąca sieć wirtualna**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Nazwa istniejącej sieci wirtualnej | Nazwa istniejącej sieci wirtualnej |
| Nazwa podsieci dla węzłów głównych | Nazwa istniejącej podsieci dla węzłów głównych.  Musi zawierać co najmniej 16 adresów IP i postępuj zgodnie z RFC 1918 |
| Nazwa podsieci dla infra węzłów | Nazwa istniejącej podsieci dla infra węzłów.  Musi zawierać co najmniej 32 adresów IP i postępuj zgodnie z RFC 1918 |
| Nazwa podsieci dla węzłów obliczeniowych i cns | Nazwa istniejącej podsieci dla węzłów obliczeniowych i cns.  Musi zawierać co najmniej 32 adresów IP i postępuj zgodnie z RFC 1918 |
| Grupy zasobów dla istniejącej sieci wirtualnej | Nazwa grupy zasobów zawierającej istniejącej sieci wirtualnej |

   ![Oferuje infrastrukturę istniejącej sieci wirtualnej](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Zakres adresów IP niestandardowe**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Zakres adresów sieci wirtualnej | Niestandardowe CIDR dla sieci wirtualnej |
| Zakres adresów podsieci zawierającej węzły główne | Niestandardowe CIDR podsieci master |
| Zakres adresów podsieci zawierającej węzłów infrastruktury | Niestandardowe CIDR podsieci infrastruktury |
| Zakres adresów podsieci zawierającej węzłów obliczeniowych i cns | Niestandardowe CIDR dla węzłów obliczeniowych i cns |

   ![Oferuje infrastrukturę niestandardowy zakres adresów IP](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Wprowadź wartości dla parametrów danych wejściowych, a następnie kliknij przycisk **OK**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Hasło użytkownika będącego administratorem platformy OpenShift | Hasło dla początkowego użytkownika OpenShift.  Ten użytkownik będzie również administratora klastra |
| Potwierdź hasło użytkownika będącego administratorem platformy OpenShift | Wpisz ponownie hasło użytkownika będącego administratorem platformy OpenShift |
| Nazwa użytkownika usługi Red Hat Menedżera subskrypcji | Nazwa użytkownika, dostęp do programu Red Hat subskrypcji lub identyfikatora organizacji.  To poświadczenie jest używane do rejestrowania wystąpienia RHEL do Twojej subskrypcji i nie będą przechowywane przez firmę Microsoft lub Red Hat |
| Hasło użytkownika w usłudze Red Hat Menedżera subskrypcji | Hasło dostępu do usługi Red Hat subskrypcji lub klucza aktywacji.  To poświadczenie jest używane do rejestrowania wystąpienia RHEL do Twojej subskrypcji i nie będą przechowywane przez firmę Microsoft lub Red Hat |
| Identyfikator puli OpenShift w usłudze Red Hat Menedżer subskrypcji | Identyfikator puli, który zawiera uprawnienie OpenShift Container Platform. Upewnij się, że masz wystarczająco dużo uprawnień OpenShift Container Platform dla instalacji klastra |
| Identyfikator puli OpenShift w usłudze Red Hat subskrypcji Menedżera brokera / Master węzłów | Pula identyfikator, który zawiera OpenShift Container Platform uprawnień dla brokera / węzły główne. Upewnij się, że masz wystarczająco dużo uprawnień OpenShift Container Platform dla instalacji klastra. Jeśli nie używa brokera / master identyfikator puli, należy wprowadzić identyfikator puli aplikacji węzłów |
| Konfigurowanie dostawcy chmury platformy Azure | Skonfiguruj OpenShift do używania dostawcy chmury platformy Azure. Wymagane, jeśli za pomocą dysku platformy Azure dołączać trwałego woluminów.  Domyślna to Yes |
| Azure identyfikator GUID klienta nazwy głównej usługi AD | Usługa Azure jednostki usługi AD klienta identyfikator GUID - znany także jako identyfikator aplikacji. Wymagane tylko, jeśli Konfigurowanie dostawcy chmury platformy Azure jest równa **tak** |
| Usługa Azure klucz tajny identyfikator klienta jednostki usługi AD | Usługa Azure jednostki usługi AD identyfikator klucza tajnego klienta. Wymagane tylko, jeśli Konfigurowanie dostawcy chmury platformy Azure jest równa **tak** |
 
   ![Oferta platformy OpenShift bloku](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Dodatkowe ustawienia**

W bloku ustawienia dodatkowe umożliwia konfigurację CNS magazynu glusterfs domeny rejestrowania, metryki i Router Sub.  Domyślnie nie można zainstalować dowolny z tych opcji i użyje nip.io jako router domeny podrzędnej do celów testowych. Włączanie CNS zainstaluje trzy węzły obliczeniowe przy użyciu trzech dodatkowych dołączonych dysków, które będą obsługiwać zasobników glusterfs.  

Wprowadź wartości dla parametrów danych wejściowych, a następnie kliknij przycisk **OK**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Konfigurowanie kontenera magazynu natywnego (CNS) | Instaluje CNS OpenShift i udostępnić go jako magazynu klastra. Zostaną domyślne, jeśli dostawca usługi Azure jest wyłączona |
| Konfiguruj rejestrowanie klastra | Instaluje EFK funkcji logowania do klastra.  Rozmiar infra węzłów odpowiednio do zasobników EFK hosta |
| Skonfiguruj metryki klastra | Instaluje Hawkular metryki w klastrze platformy OpenShift.  Rozmiar infra węzłów odpowiednio do zasobników Hawkular metryki hosta |
| Domyślny routera poddomena | Wybierz nipio do testowania lub niestandardowych do wprowadź domenę sub w środowisku produkcyjnym |
 
   ![Blok oferty](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Dodatkowe ustawienia — dodatkowe parametry**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| (CNS) Rozmiar węzła | Zaakceptuj domyślny rozmiar węzła lub wybierz **zmiana rozmiaru** aby wybrać nowy rozmiar maszyny Wirtualnej |
| Wprowadź swoje niestandardowe poddomeny | Niestandardowe domeny routingu ma być używany do udostępniania aplikacji za pośrednictwem routera w klastrze platformy OpenShift.  Pamiętaj utworzyć wpis DNS odpowiednim symbolem wieloznacznym] |
 
   ![Oferuje dodatkowe cns instalacji](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Podsumowanie**

Sprawdzanie poprawności jest wykonywane na tym etapie w celu sprawdzenia, czy limit przydziału rdzeni jest wystarczające, aby wdrożyć łączna liczba maszyn wirtualnych wybrane do klastra.  Sprawdź wszystkie parametry, które zostały wprowadzone.  Jeśli dane wejściowe są akceptowane, kliknij przycisk **OK** aby kontynuować.

   ![Blok podsumowania oferty](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Buy**

Upewnij się, na stronie kupić dane kontaktowe, a następnie kliknij przycisk **zakupu** aby zaakceptować warunki użytkowania i rozpocząć wdrażanie klastra OpenShift Container Platform.

   ![Oferty zakupu bloku](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Nawiąż połączenie z klastrem platformy OpenShift

Po zakończeniu wdrożenia, należy pobrać połączenie z sekcji wyjściowej wdrożenia. Łączenie z konsolą OpenShift za pomocą przeglądarki przy użyciu **adres URL konsoli OpenShift**. Można również SSH z hostem bastionu. Poniżej znajduje się przykład, w którym nazwa użytkownika administratora jest clusteradmin i publiczny adres IP bastionu pełni kwalifikowaną nazwę domeny DNS jest bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure](./openshift-troubleshooting.md)
- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform)

