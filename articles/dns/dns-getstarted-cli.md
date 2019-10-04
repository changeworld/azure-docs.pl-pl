---
title: Szybki Start — tworzenie strefy Azure DNS i rekordu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przewodnik Szybki Start — informacje na temat tworzenia strefy i rekordu DNS w Azure DNS. Jest to przewodnik krok po kroku dotyczący tworzenia i zarządzania pierwszą strefą i rekordem DNS przy użyciu interfejsu wiersza polecenia platformy Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b5d842c2d6ff84a0f17c4e8be0bfade018edc48b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959973"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Szybki Start: Tworzenie strefy Azure DNS i rekordu przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule omówiono procedurę tworzenia pierwszej strefy i rekordu DNS przy użyciu interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Te kroki można również wykonać przy użyciu [Azure Portal](dns-getstarted-portal.md) lub [Azure PowerShell](dns-getstarted-powershell.md).

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć Hostowanie domeny w Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS dla domeny zostanie utworzony w tej strefie DNS. Na koniec aby opublikować strefę DNS w Internecie, należy skonfigurować serwery nazw dla domeny. Każdy z tych kroków został opisany poniżej.

Azure DNS obsługuje również prywatne strefy DNS. Aby dowiedzieć się więcej o prywatnych strefach DNS, zobacz [używanie Azure DNS w przypadku domen prywatnych](private-dns-overview.md). Aby uzyskać przykład sposobu tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy z Azure DNS strefami prywatnymi przy użyciu interfejsu wiersza polecenia](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć grupę zasobów zawierającą strefę DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia `az network dns zone create`. Aby wyświetlić pomoc dla tego polecenia, wpisz `az network dns zone create -h`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso. xyz* *w grupie zasobów*. Użyj tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network dns record-set [record type] add-record`. Aby uzyskać pomoc dotyczącą rekordów, zobacz `azure network dns record-set A add-record -h`.

W poniższym przykładzie jest tworzony rekord o nazwie względnej "www" w strefie DNS "contoso. xyz" w grupie zasobów "zasób". W pełni kwalifikowana nazwa zestawu rekordów to "www. contoso. xyz". Typ rekordu to "A" z adresem IP "10.10.10.10" i domyślny czas wygaśnięcia wynoszący 3600 sekund (1 godzina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Wyświetl rekordy

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testowanie rozpoznawania nazw

Teraz, gdy istnieje testowa strefa DNS z rekordem testowym "A", można testować rozpoznawanie nazw za pomocą narzędzia o nazwie *nslookup*. 

**Aby przetestować rozpoznawanie nazw DNS:**

1. Uruchom następujące polecenie cmdlet, aby uzyskać listę serwerów nazw dla strefy:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Skopiuj jedną z nazw serwerów nazw z danych wyjściowych poprzedniego kroku.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Na przykład:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Powinien wyglądać podobnie do następującego ekranu:

   ![polecenia](media/dns-getstarted-portal/nslookup.PNG)

Nazwa hosta **www\.contoso.xyz** jest rozpoznawana jako **10.10.10.10**, tak jak została skonfigurowana. Powoduje to sprawdzenie, czy rozpoznawanie nazw działa poprawnie.

## <a name="delete-all-resources"></a>Usuń wszystkie zasoby

Gdy nie jest już potrzebne, możesz usunąć wszystkie zasoby utworzone w tym przewodniku Szybki Start, usuwając grupę zasobów:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu pierwszej strefy i rekordu DNS przy użyciu interfejsu wiersza polecenia platformy Azure można tworzyć rekordy aplikacji sieci Web w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Tworzenie rekordów DNS dla aplikacji sieci Web w domenie niestandardowej](./dns-web-sites-custom-domain.md)
