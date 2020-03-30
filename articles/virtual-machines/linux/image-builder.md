---
title: Tworzenie maszyny Wirtualnej systemu Linux za pomocą usługi Azure Image Builder (wersja zapoznawcza)
description: Utwórz maszynę wirtualną z systemem Linux za pomocą konstruktora obrazów platformy Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066685"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Wersja zapoznawcza: tworzenie maszyny Wirtualnej systemu Linux za pomocą usługi Azure Image Builder

W tym artykule pokazano, jak można utworzyć dostosowany obraz systemu Linux przy użyciu usługi Azure Image Builder i interfejsu wiersza polecenia platformy Azure. W tym artykule użyto trzech różnych [konfiguratorów](image-builder-json.md#properties-customize) do dostosowywania obrazu:

- Shell (ScriptUri) - pobiera i uruchamia [skrypt powłoki](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - uruchamia określone polecenia. W tym przykładzie polecenia wbudowane obejmują tworzenie katalogu i aktualizowanie systemu operacyjnego.
- Plik — kopiuje [plik z github](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) do katalogu na maszynie Wirtualnej.

Można również określić plik `buildTimeoutInMinutes`. Wartość domyślna to 240 minut i można zwiększyć czas kompilacji, aby umożliwić dłuższe uruchamianie kompilacji.

Będziemy używać przykładowego szablonu .json do skonfigurowania obrazu. Plik .json, z których korzystamy, jest tutaj: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Zarejestruj funkcje
Aby korzystać z usługi Azure Image Builder podczas podglądu, należy zarejestrować nową funkcję.

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

Jeśli nie mówią zarejestrowane, uruchom następujące czynności:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>Konfigurowanie przykładowych zmiennych

Będziemy wielokrotnie wykorzystywać niektóre informacje, więc utworzymy pewne zmienne do przechowywania tych informacji.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Utwórz grupę zasobów.
Służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ustawianie uprawnień do grupy zasobów
Nadaj kreatorowi obrazów uprawnienie "współautor" do utworzenia obrazu w grupie zasobów. Bez odpowiednich uprawnień kompilacja obrazu zakończy się niepowodzeniem. 

Wartość `--assignee` jest identyfikatorem rejestracji aplikacji dla usługi Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Pobierz przykład szablonu

Został utworzony szablon konfiguracji obrazu przykładowego sparametryzowany. Pobierz przykładowy plik .json i skonfiguruj go przy tak z ustawionymi wcześniej zmiennymi.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

W razie potrzeby można zmodyfikować ten przykład .json. Na przykład można zwiększyć `buildTimeoutInMinutes` wartość, aby umożliwić dłuższe uruchamianie kompilacji. Plik można edytować w usłudze Cloud `vi`Shell za pomocą edytora tekstu, takiego jak .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> W przypadku obrazu źródłowego należy zawsze [określić wersję,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)której nie można użyć `latest`.
>
> Jeśli dodasz lub zmienisz grupę zasobów, w której obraz jest dystrybuowany, musisz upewnić się, że [uprawnienia są ustawione dla grupy zasobów](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Prześlij konfigurację obrazu
Przesyłanie konfiguracji obrazu do usługi Konstruktora obrazów maszyn wirtualnych

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Jeśli zakończy się pomyślnie, zwróci komunikat o powodach i utworzy artefakt szablonu konstruktora obrazów w $imageResourceGroup. Możesz zobaczyć grupę zasobów w portalu, jeśli włączysz "Pokaż ukryte typy".

Ponadto w tle Konstruktor obrazów tworzy grupę zasobów przejściowych w ramach subskrypcji. Konstruktor obrazów używa tymczasowej grupy zasobów dla kompilacji obrazu. Nazwa grupy zasobów będzie w tym `IT_<DestinationResourceGroup>_<TemplateName>`formacie: .

> [!IMPORTANT]
> Nie należy bezpośrednio usuwać przemieszczania grupy zasobów. Usunięcie artefaktu szablonu obrazu spowoduje automatyczne usunięcie grupy zasobów przemieszczania. Aby uzyskać więcej informacji, zobacz [Sekcję Oczyszczanie](#clean-up) na końcu tego artykułu.

Jeśli usługa zgłasza błąd podczas przesyłania szablonu konfiguracji obrazu, zobacz kroki [rozwiązywania problemów.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Przed ponoweniem przed przesłaniem kompilacji należy również usunąć szablon. Aby usunąć szablon:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Uruchamianie kompilacji obrazu

Rozpocznij kompilację obrazu.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Poczekaj, aż kompilacja zostanie ukończona, w tym przykładzie może upłynąć 10-15 minut.

Jeśli wystąpią jakiekolwiek błędy, zapoznaj się z tymi krokami [rozwiązywania problemów.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu utworzonego obrazu.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Pobierz adres IP z danych wyjściowych tworzenia maszyny Wirtualnej i użyj go do SSH do maszyny Wirtualnej.

```bash
ssh azureuser@<pubIp>
```

Powinieneś zobaczyć obraz został dostosowany z Wiadomością dnia, gdy tylko połączenie SSH zostanie nawiązane!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Wpisz, `exit` gdy skończysz, aby zamknąć połączenie SSH.

## <a name="check-the-source"></a>Sprawdź źródło

W szablonie konstruktora obrazów w "Właściwościach" zobaczysz obraz źródłowy, skrypt dostosowywania, który działa, i gdzie jest dystrybuowany.

```bash
cat helloImageTemplateLinux.json
```

Aby uzyskać bardziej szczegółowe informacje na temat tego pliku json, zobacz [Odwołanie do szablonu konstruktora obrazów](image-builder-json.md)

## <a name="clean-up"></a>Czyszczenie

Po zakończeniu można usunąć zasoby.

Usuń szablon konstruktora obrazów.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Usuń grupę zasobów obrazu.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku .json użytego w tym artykule, zobacz [Odwołanie do szablonu Konstruktora obrazów](image-builder-json.md).
