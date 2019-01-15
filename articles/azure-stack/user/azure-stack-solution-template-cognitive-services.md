---
title: Wdrażanie usług Azure Cognitive Services do usługi Azure Stack | Dokumentacja firmy Microsoft
description: Informacje o wdrażaniu usług Azure Cognitive Services do usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.openlocfilehash: bf056c162684c021e3a8408edfdf82fe4590be48
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260638"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Wdrażanie usług Azure Cognitive Services do usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Usługi Azure Cognitive Services w usłudze Azure Stack jest w wersji zapoznawczej.

Za pomocą usług Azure Cognitive Services obsługi kontenerów w usłudze Azure Stack. Obsługa kontenerów w usługach Azure Cognitive Services pozwala używać tych samych zaawansowanych interfejsów API, które są dostępne na platformie Azure. Korzystanie z kontenerów powala w przypadku, gdy do wdrażania i obsługi usługi zapewniane przez [kontenerów platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna w wersji zapoznawczej dla podzbioru usług Azure Cognitive Services, łącznie z części [komputerowej](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview), i [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), i [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Konteneryzacji to podejście do dystrybucji oprogramowania, w którym aplikacji lub usługi, takie jak jego zależności i konfiguracji, są dostarczane w postaci obrazu kontenera. Za pomocą niewielkie modyfikacje można wdrożyć obraz hosta kontenera. Każdy kontener jest odizolowana od innych kontenerów i od zasadniczego systemu operacyjnego. Sam, system musi tylko składniki potrzebne do uruchomienia obrazu. Host kontenera ma mniejszy wyświetlacz niż maszyny wirtualnej. Ponadto możesz tworzyć kontenery z obrazów krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Używać kontenerów za pomocą usług Cognitive Services w usłudze Azure Stack

- **Kontroli nad danymi**  
  Użytkownicy aplikacji mogą mieć kontrolę nad ich danymi podczas korzystania z usług Cognitive Services. Usługi Cognitive Services można dostarczyć do użytkowników aplikacji, którzy nie mogą wysyłać dane do globalnej platformy Azure lub w chmurze publicznej.

- **Kontrola nad aktualizacji modelu**  
  Zapewnić użytkownikom aplikacji do wersji i aktualizowanie modeli wdrożone w swoich rozwiązaniach.

- **Architektura przenośny**  
  Umożliwiają tworzenie architektury aplikacji przenośne tak, aby wdrożyć dane rozwiązanie chmury publicznej, prywatnej chmury w środowisku lokalnym lub krawędzi. Możesz wdrożyć kontenera do usługi Azure Kubernetes Service, Azure Container Instances, lub do klastra Kubernetes w usłudze Azure Stack. Aby uzyskać więcej informacji, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

- **Wysoka przepływność i małe opóźnienia**  
   Umożliwia użytkownikom aplikacji skalowanie za pomocą skoków ruchu dla wysokiej przepływności i małego opóźnienia. Włączanie usług Cognitive Services do uruchamiania w usłudze Azure Kubernetes Service fizycznie blisko ich logiki aplikacji i danych.

Dzięki usłudze Azure Stack wdrażanie kontenerów usług Cognitive Services, w klastrze Kubernetes wraz z kontenerów aplikacji o wysokiej dostępności i elastyczne skalowanie. Możesz tworzyć aplikacji, łącząc usług Cognitive services ze składnikami w oparciu App Services, Functions, Blob storage i SQL lub mySQL baz danych. 

Aby uzyskać szczegółowe informacje na temat usług Cognitive Services, kontenerów, przejdź do [obsługi kontenerów w usługach Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Wdrażanie usługi Azure interfejs API rozpoznawania twarzy

W tym artykule opisano sposób wdrażania w klastrze Kubernetes w usłudze Azure Stack — interfejs API rozpoznawania twarzy na platformie Azure. To samo podejście można użyć do wdrożenia innych kontenerów usług cognitive services w klastrze usługi Azure Stack w usłudze Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

1.  Żądanie dostępu do rejestru kontenerów do ściągania obrazów kontenera twarzy z usługi Azure Cognitive Services Container Registry. Aby uzyskać szczegółowe informacje, przejdź do sekcji [żądanie dostępu do prywatnego rejestru kontenerów](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Przygotuj klaster Kubernetes w usłudze Azure Stack. Można wykonać tego artykułu [wdrażanie platformy Kubernetes w usłudze Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Utwórz zasób usługi cognitive Services na platformie Azure nad wersją zapoznawczą kontenery rozpoznawania twarzy, LUIS lub rozpoznawanie tekstu, odpowiednio. Należy użyć subskrypcji key i punktu końcowego adresu URL z zasobu do utworzenia wystąpienia kontenerów usługi cognitive Services.

1.  Tworzenie zasobu platformy Azure w witrynie Azure portal. Aby wyświetlić podgląd kontenery twarzy, możesz najpierw utworzyć odpowiadający jej zasób twarzy w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Utwórz konto usług Cognitive Services w witrynie Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

    >  [!Note]  
    >  Zasób twarzy lub przetwarzania obrazów musi używać F0 warstwę cenową.

2.  Pobieranie klucza punktu końcowego adresu URL i subskrypcji dla zasobów platformy Azure. Po utworzeniu zasobu platformy Azure należy użyć subskrypcji klucz i punkt końcowy adres URL z tego zasobu do utworzenia wystąpienia odpowiedniego kontenera rozpoznawania twarzy, LUIS lub rozpoznawanie tekstu dla wersji zapoznawczej.

## <a name="create-a-kubernetes-secret"></a>Tworzenie wpisu tajnego rozwiązania Kubernetes 

Wypełnij użytkowania Kubectl Tworzenie wpisu tajnego polecenie, aby dostęp do prywatnego rejestru kontenerów. Zastąp **&lt;username&gt;** z nazwą użytkownika i **&lt;hasło&gt;** przy użyciu hasła podanego w poświadczeniu otrzymany od platformy Azure Zespołu cognitive Service.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Przygotowanie YAML Konfigurowanie pliku

Zamierzasz używać YAML skonfigurować plik, aby uprościć wdrażanie cognitive service w klastrze usługi Kubernetes.

Poniżej przedstawiono przykładowe YAML skonfigurować plik, aby wdrożyć usługę rozpoznawania twarzy do usługi Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

W tym YAML skonfigurować plik, należy użyć hasła, które umożliwia uzyskiwanie usługi cognitive Services obrazów kontenera usługi Azure Container Registry. Możesz i użyj plik wpisów tajnych się wdrożenie repliki określonego kontenera. Możesz również utworzyć równoważenia obciążenia do upewnij się, że użytkownicy mogą uzyskiwać dostęp do tej usługi na zewnątrz.

Szczegółowe informacje na temat pól klucza:

| Pole | Uwagi |
| --- | --- |
| replicaNumber | Definiuje początkowej repliki wystąpienia do utworzenia. Można ją z pewnością skalować później, po wdrożeniu. |
| ImageLocation | Wskazuje lokalizację obrazu kontenera określonej usługi cognitive Services w rejestru Azure container Registry. Na przykład usługa rozpoznawania twarzy: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Adres URL punktu końcowego zanotowanym w kroku [tworzenie zasobów platformy Azure](#create-azure-resources) |
| ApiKey | Klucz subskrypcji zanotowanym w kroku [tworzenie zasobów platformy Azure](#create-azure-resources) |
| SecretName | Nazwa wpisu tajnego, po prostu zanotowaną w kroku [Utwórz secrete do dostępu do prywatnego rejestru kontenerów](#create-secrete-to-access-the-private-container-registry) |

## <a name="deploy-the-cognitive-service"></a>Wdrażanie usługi cognitive

Użyj następującego polecenia, należy wdrażać kontenery usługi cognitive Services

```bash  
    Kubectl apply -f <yamlFineName>
```
Użyj następującego polecenia, aby monitorować, jak wdraża: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testowanie usług cognitive

Dostęp do [specyfikacji interfejsu OpenAPI](https://swagger.io/docs/specification/about/) (dawniej specyfikacją struktury Swagger), opisujący operacje obsługiwane przez kontener wystąpień z **/swagger** względny identyfikator URI dla tego kontenera. Na przykład następujący identyfikator URI zapewnia dostęp do specyfikacji interfejsu OpenAPI dla kontenera analiza tonacji, do którego został utworzony w poprzednim przykładzie:

```HTTP  
http:<External IP>:5000/swagger
```

Zewnętrzny adres IP można uzyskać z następującego polecenia: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Wypróbuj usługi za pomocą języka Python

Możesz spróbować zweryfikować usług Cognitive services na usługi Azure Stack, uruchamiając kilka prostych skryptów języka Python. Istnieją oficjalne przykłady w języku Python Szybki Start dla [komputerowej](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview), i [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), i [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) () Usługa LUIS) dla Twojej informacji.

Istnieją dwie rzeczy konieczność należy pamiętać, aby zapewnić aplikacji języka Python, uruchamiać usługi działające na kontenerach: 
1. Usługi cognitive services w kontenerze nie ma potrzeby podkluczy dla uwierzytelniania, ale wciąż potrzebujemy wprowadzanie dowolny ciąg jako symbol zastępczy w celu zaspokojenia zestawu SDK. 
2. Zamień na adres IP punktu końcowego usługi rzeczywiste base_URL 

Poniżej przedstawiono przykładowe Python skryptu używanego twarzy usług zestawu SDK języka Python do wykrywania i ramki twarzy na obrazie. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Kolejne kroki

[Jak zainstalować i uruchomić interfejs API przetwarzania obrazów kontenerów.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Jak zainstalować i uruchamiać kontenery interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[Jak zainstalować i uruchamiać kontenery interfejsu API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Jak zainstalować i uruchamiać kontenery Language Understanding (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)