---
title: "Odwołanie do interfejsu wiersza polecenia Azure Machine Learning Model zarządzania | Dokumentacja firmy Microsoft"
description: "Dokumentacja interfejsu wiersza polecenia Azure Machine Learning Model zarządzania."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 27361c5b92a8748a026d457875fadfc1f3529076
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="model-management-command-line-interface-reference"></a>Odwołanie do modelu administracyjnego interfejsu wiersza polecenia

## <a name="base-cli-concepts"></a>Podstawowe pojęcia interfejsu wiersza polecenia:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Konto polecenia
Konto zarządzania model jest wymagany do korzystania z usług, które umożliwiają wdrażanie i zarządzanie nimi modeli. Użyj `az ml account modelmanagement -h` wyświetlić na poniższej liście:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Utwórz konto zarządzania modelu**

Utwórz konto zarządzania modelu rozliczeń za pomocą następującego polecenia:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Argumenty lokalnego:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Polecenia środowiska

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Konfigurowanie środowiska wdrażania**

Polecenia instalacji wymagane jest posiadanie współautora dostępu do subskrypcji. Jeśli go nie masz, potrzebujesz przynajmniej prawa dostępu współautora do grupy zasobów, w której przeprowadzasz wdrożenie. W drugim przypadku musisz określić nazwę grupy zasobów jako część polecenia instalatora przy użyciu flagi `-g`. 

Dostępne są dwie opcje wdrożenia: *lokalnego* i *klastra*. Ustawienie `--cluster` (lub `-c`) Flaga umożliwia wdrożenie klastra, który inicjuje klastrem usługi ACS. Podstawowa konfiguracja ma następującą składnię:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

To polecenie inicjuje komputerze Azure uczenia środowisko z konta magazynu, ACR rejestru i utworzono w ramach subskrypcji usługi App Insights. Domyślnie środowisko jest inicjowany dla lokalnych tylko w przypadku wdrożeń (nie ACS) Jeśli flaga nie zostanie określony. Jeśli potrzebujesz można skalować usługi, określ `--cluster` (lub `-c`) flagę, aby utworzyć klaster usług ACS.

Szczegóły polecenia:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Argumenty globalne
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Polecenia modelu

    list
    register
    show

**Zarejestruj modelu**

Polecenie, aby zarejestrować model.

`az ml model register --model [path to model file] --name [model name]`

Szczegóły polecenia:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Argumenty globalne

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Polecenia manifestu

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Tworzenie manifestu**

Poniższe polecenie tworzy plik manifestu dla modelu. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Szczegóły polecenia:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Argumenty zarejestrowanego modelu

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Argumenty wyrejestrować modelu

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Argumenty globalne

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Polecenia obrazu

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Tworzenie obrazu**

Z możliwością wytworzeniu swoim manifeście przed można utworzyć obrazu. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Lub można utworzyć manifestu i obrazu za pomocą jednego polecenia. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Szczegóły polecenia:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Zarejestrowany manifestu argumentów

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Wyrejestrować argumenty manifestu

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Polecenia usługi
Obsługiwane są następujące polecenia dla usługi. Aby wyświetlić parametry dla każdego polecenia, użyj opcji -h. Na przykład użyć `az ml service create realtime -h` aby zobaczyć szczegóły polecenia Utwórz.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Tworzenie usługi**

Tworzenie usługi przy użyciu utworzonego wcześniej obrazu, użyj następującego polecenia:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Aby utworzyć usługę, manifestu i obrazów za pomocą jednego polecenia następujące polecenie:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Szczegóły poleceń:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Argumenty zarejestrowanych obrazu

    --image-id                        : [Required] Image to deploy to the service.

Argumenty wyrejestrować obrazu

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Argumenty globalne

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Należy zwrócić uwagę na `-d` flagę dołączanie zależności: w przypadku przekazania nazwę katalogu, który nie jest już powiązane (zip, tar itp.), ten katalog jest automatycznie pobiera tar'ed i jest przekazywany wraz, a następnie automatycznie oddzielona po drugiej stronie. 

W przypadku przekazania w katalogu, który jest już powiązane, katalog jest traktowany jako plik i przekazywane jest. Nieograniczony jest automatycznie; Oczekiwano obsługi, który w kodzie.

**Pobierz szczegóły usługi**

Pobierz szczegóły usługi, łącznie z adresem URL użycia (łącznie przykładowych danych, jeśli schemat został utworzony).

`az ml service show realtime --name [service name]`

Szczegóły polecenia:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumenty globalne

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Uruchom usługę**

`az ml service run realtime -n [service name] -d [input_data]`

Szczegóły polecenia:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumenty globalne

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Polecenie

    az ml service run realtime

Argumenty — identyfikator -i: [wymagane] wynik względem tego identyfikatora usługi.
-d: dane do użycia podczas wywoływania usługi sieci web.
-v: Flaga szczegółowości.

Argumenty globalne

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
