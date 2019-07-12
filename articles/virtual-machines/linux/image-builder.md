---
title: Tworzenie maszyny Wirtualnej z systemem Linux przy użyciu Kreatora obrazów platformy Azure (wersja zapoznawcza)
description: Utwórz Maszynę wirtualną systemu Linux za pomocą Konstruktora obrazów systemu Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667524"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Wersja zapoznawcza: Tworzenie maszyny Wirtualnej z systemem Linux przy użyciu Kreatora obrazów platformy Azure

W tym artykule przedstawiono sposób tworzenia dostosowanego obrazu systemu Linux przy użyciu Kreatora obrazów platformy Azure i wiersza polecenia platformy Azure. W przykładzie w tym artykule używa trzech różnych [twórcom dostosowań](image-builder-json.md#properties-customize) dostosowywania obrazu:

- Shell (ScriptUri) — pliki do pobrania i uruchomienia [skrypt powłoki](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (wbudowane) — określone polecenia uruchomienia. W tym przykładzie polecenia wbudowanego obejmują tworzenie katalogu i aktualizowanie systemu operacyjnego.
- Plik — kopiuje [plik z repozytorium GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do katalogu na maszynie Wirtualnej.


Firma Microsoft będzie używać przykładowy szablon JSON do obrazu. Użyto pliku JSON jest tutaj: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Kreator obrazów platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby użyć Kreatora obrazów platformy Azure w wersji zapoznawczej, musisz zarejestrować nową funkcję.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Sprawdź swoją rejestrację.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie mówią zarejestrowanych, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>Konfigurowanie zmiennych przykład

Firma Microsoft będzie używać niektórych rodzajów informacji wielokrotnie, dlatego utworzymy pewne zmienne do przechowywania tych informacji.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Utwórz zmienną dla Twojego identyfikatora subskrypcji. Można uzyskać, to przy użyciu `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Utwórz grupę zasobów.
Służy do przechowywania artefaktów szablonu konfiguracji obrazu i obraz.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ustaw uprawnienia dla grupy zasobów
Nadaj uprawnienia "Współautor" kreatora obrazów do utworzenia obrazu w grupie zasobów. Bez odpowiednich uprawnień budowania obrazu zakończy się niepowodzeniem. 

`--assignee` Wartość Identyfikatora rejestracji aplikacji dla usługi kreatora obrazów. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Pobierz przykład szablonu

Sparametryzowany przykładowy obraz konfiguracji utworzono szablon do użycia. Pobierz przykładowy plik JSON, a następnie skonfigurować go przy użyciu zmiennych, które zostały ustawione wcześniej.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Ten przykład JSON można zmodyfikować zgodnie z potrzebami. Na przykład, można zwiększyć wartość `buildTimeoutInMinutes` umożliwiające dłuższe uruchomionej kompilacji. Możesz edytować plik w usłudze Cloud Shell przy użyciu `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Źródło obrazu, należy zawsze [Określ wersję](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), nie można użyć `latest`.
>
> W przypadku dodania lub Zmień grupę zasobów, w miejsce dystrybucji obrazu należy upewnić się, że [uprawnień ustawionych dla grupy zasobów](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Prześlij konfigurację obrazów
Prześlij konfigurację obrazu, aby usługa kreatora obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Po pomyślnym ukończeniu, go zwróci komunikat o powodzeniu i utworzyć obraz konstruktora konfiguracji szablonu artefakt w $imageResourceGroup. Grupy zasobów w portalu możesz zobaczyć, jeśli zostanie włączone ustawienie "Pokaż ukryte typy".

Ponadto w tle, Kreator obrazów tworzy tymczasową grupy zasobów w ramach subskrypcji. Kreator obrazów używa przemieszczania grupy zasobów do budowania obrazu. Nazwa grupy zasobów będzie mieć następujący format: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Nie usuwaj przemieszczania grupę zasobów bezpośrednio. Jeśli usuniesz artefaktu szablon obrazu, automatycznie usunie przemieszczania grupy zasobów. Aby uzyskać więcej informacji, zobacz [wyczyścić](#clean-up) sekcji na końcu tego artykułu.

Jeśli usługa zgłasza błąd podczas przesyłania obrazów konfiguracji szablonu, zobacz [Rozwiązywanie problemów z](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) kroki. Należy również usunąć szablon przed ponowieniem próby przesyłanie kompilacji. Aby usunąć szablon:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchom kompilację obrazu

Uruchom kompilację obrazu.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Poczekaj, aż kompilacja zostanie zakończona, w taki sposób, w tym przykładzie, może potrwać 10 – 15 minut.

Jeśli wystąpią jakiekolwiek problemy, przejrzyj te [Rozwiązywanie problemów z](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) kroki.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny Wirtualnej przy użyciu tworzonej obrazu.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Uzyskaj adres IP z danych wyjściowych tworzenia maszyny Wirtualnej i przy jego użyciu SSH z maszyną Wirtualną.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Powinien pojawić się obraz, który został dostosowany komunikatem dnia, jak najszybciej po nawiązaniu połączenia SSH!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` po zakończeniu zamknij połączenie SSH.

## <a name="check-the-source"></a>Sprawdź źródłowy

W szablonie konstruktora obraz w programie "Properties", zobaczysz obrazu źródłowego, dostosowywania skryptu przebiegów, gdzie jest dystrybuowane.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Aby uzyskać szczegółowe informacje o tym pliku JSON, zobacz [Image builder odwołanie do szablonu](image-builder-json.md)

## <a name="clean-up"></a>Czyszczenie

Gdy wszystko będzie gotowe, możesz usunąć zasoby.

Usuń szablon konstruktora obrazu.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Usuń grupę zasobów obrazu.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach plik JSON używanych w tym artykule, zobacz [odwołanie do szablonu Kreatora obrazów](image-builder-json.md).
