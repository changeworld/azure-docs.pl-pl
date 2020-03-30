---
title: 'Szybki start: tworzenie strefy DNS platformy Azure i rejestrowanie — narzędzie interfejsu wiersza polecenia platformy Azure'
titleSuffix: Azure DNS
description: Szybki start — dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę i rekord DNS przy użyciu interfejsu wiersza polecenia platformy Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e6904c013cf2ed897bdc7c8b32f04fe500fc31d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76937196"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Szybki start: tworzenie strefy i rekordu usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule przedstawiono procedurę tworzenia po raz pierwszy strefy i rekordu DNS przy użyciu interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Te kroki można również wykonać przy użyciu witryny [Azure Portal](dns-getstarted-portal.md) lub programu [Azure PowerShell](dns-getstarted-powershell.md).

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby na koniec opublikować strefę DNS w Internecie, należy skonfigurować serwery nazw dla domeny. Poniżej opisano każdy z tych kroków.

Usługa Azure DNS obsługuje również prywatne strefy DNS. Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md). Aby zapoznać się z przykładowym sposobem tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu interfejsu wiersza polecenia](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Do tworzenia strefy DNS służy polecenie `az network dns zone create`. Aby wyświetlić pomoc dla tego polecenia, wpisz `az network dns zone create -h`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.xyz* w grupie zasobów *MyResourceGroup*. Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network dns record-set [record type] add-record`. Aby uzyskać pomoc dotyczącą rekordów A, zobacz `azure network dns record-set A add-record -h`.

Poniższy przykład tworzy rekord o względnej nazwie "www" w strefie DNS "contoso.xyz" w grupie zasobów "MyResourceGroup". W pełni kwalifikowaną nazwą zestawu rekordów jest "www.contoso.xyz". Typ rekordu to "A", z adresem IP "10.10.10.10", a domyślny czas wygaśnięcia 3600 sekund (1 godzina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testowanie rozpoznawania nazw

Po utworzeniu testowej strefy DNS z rekordem „A” możesz przetestować rozpoznawanie nazw za pomocą narzędzia o nazwie *nslookup*. 

**Aby przetestować rozpoznawanie nazw DNS:**

1. Uruchom następujące polecenie cmdlet, aby uzyskać listę serwerów nazw dla swojej strefy:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Skopiuj jedną z nazw serwerów nazw z danych wyjściowych poprzedniego kroku.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Przykład:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Powinna zostać wyświetlona treść podobna do tej na następującym ekranie:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Nazwa hosta **www\.contoso.xyz** jest rozpoznawana jako **10.10.10.10**, tak jak została skonfigurowana. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie.

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki start nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu strefy i rekordu DNS po raz pierwszy przy użyciu interfejsu wiersza polecenia platformy Azure możesz utworzyć rekordy dla aplikacji internetowej w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
