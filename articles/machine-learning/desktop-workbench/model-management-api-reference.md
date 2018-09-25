---
title: Tworzenie obrazu platformy Docker do zarządzania modelami w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: W tym artykule opisano kroki tworzenia obrazu platformy Docker dla usługi sieci web.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 07f8a31244acca48a0bbae8ad26b0c5b509f0c0a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959584"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Dokumentacja usługi Azure Machine Learning Model zarządzania konta API

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Aby uzyskać informacji na temat konfigurowania środowiska wdrażania, zobacz [Konfiguracja konta zarządzania modelami](deployment-setup-configuration.md).

Azure Machine Learning Model zarządzania konta API implementuje są następujące operacje:

- Rejestracja modelu
- Tworzenie manifestu
- Tworzenie obrazu platformy docker
- Tworzenie usługi sieci Web

Ten obraz umożliwia tworzenie usługi internetowej albo lokalnie lub na zdalny klaster usługi kontenera platformy Azure lub innym z obsługą platformy Docker wybranym środowisku.

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że wykonano już kroki instalacji w [Instalowanie i Tworzenie szybkiego startu](quickstart-installation.md) dokumentu.

Wymagane jest spełnienie następujących przed kontynuowaniem:
1. Model, aprowizacja kont zarządzania
2. Tworzenie środowiska wdrażania modeli i zarządzania
3. Model usługi Machine Learning

### <a name="azure-ad-token"></a>Tokenu usługi Azure AD
Podczas korzystania z wiersza polecenia platformy Azure, zaloguj się przy użyciu `az login`. Interfejs wiersza polecenia używa tokenu usługi Azure Active Directory (Azure AD) z pliku .azure. Jeśli chcesz korzystać z interfejsów API, masz następujące opcje.

##### <a name="acquire-the-azure-ad-token-manually"></a>Ręcznie pobrać tokenu usługi Azure AD
Możesz użyć `az login` i Pobierz najnowsze token z pliku .azure w katalogu macierzystym.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Programowe uzyskiwanie tokenu usługi Azure AD
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Po utworzeniu nazwy głównej usługi, Zapisz dane wyjściowe. Teraz możesz go używać, aby uzyskać token z usługi Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Token jest umieszczany w nagłówku autoryzacji dla wywołań interfejsu API.


## <a name="register-a-model"></a>Zarejestruj model

W kroku rejestracji modelu rejestruje modelu uczenia maszynowego przy użyciu konta Zarządzanie modelami w usłudze, który został utworzony. Rejestracja umożliwia śledzenie modele i ich wersje, które są przypisane w czasie rejestracji. Użytkownik podaje nazwę modelu. Kolejne rejestracji modeli w ramach tej samej nazwie generuje nową wersję i identyfikator.

### <a name="request"></a>Żądanie

| Metoda | Identyfikator URI żądania |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Opis
Rejestruje modelu.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| model | treść | Ładunek, który służy do rejestrowania modelu. | Yes | [Model](#model) |


### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | OK. Rejestracja modelu powiodło się. | [Model](#model) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Na liście modeli w ramach konta kwerendy
### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Opis
Wysyła zapytanie do listy modeli w ramach konta. Można filtrować listę wyników według tagu i nazwę. Jeśli filtr nie zostanie przekazana, zapytanie Wyświetla listę wszystkich modeli w ramach konta. Zwrócona lista jest podzielony na strony, a liczba elementów na każdej stronie jest parametrem opcjonalnym.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| name | query | Nazwa obiektu. | Nie | ciąg |
| tag | query | Tag modelu. | Nie | ciąg |
| count | query | Liczba elementów do pobrania na stronie. | Nie | ciąg |
| $skipToken | query | Token kontynuacji do pobrania następnej strony. | Nie | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [PaginatedModelList](#paginatedmodellist) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Pobierz szczegóły modelu
### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Opis
Pobiera model według identyfikatora.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obiektu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [Model](#model) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Zarejestrowanie manifestu zarejestrowanego modelu i wszystkie zależności

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Opis
Rejestruje manifestu z zarejestrowanego modelu i wszystkich jego zależności.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| manifestRequest | treść | Ładunek, który służy do rejestrowania manifestu. | Yes | [Manifest](#manifest) |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Rejestracje manifestu zakończyło się pomyślnie. | [Manifest](#manifest) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Zapytanie listy manifestów na koncie usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Opis
Wysyła zapytanie do listy manifestów na koncie usługi. Można filtrować listę wyników przez model ID i nazwa manifestu. Jeśli filtr nie zostanie przekazana, w zapytaniu przedstawiono wszystkie manifesty w ramach konta. Zwrócona lista jest podzielony na strony, a liczba elementów na każdej stronie jest parametrem opcjonalnym.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| modelId | query | Identyfikator modelu. | Nie | ciąg |
| manifestName | query | Nazwa manifestu. | Nie | ciąg |
| count | query | Liczba elementów do pobrania na stronie. | Nie | ciąg |
| $skipToken | query | Token kontynuacji do pobrania następnej strony. | Nie | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Szczegółowe informacje manifestu

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Opis
Pobiera manifest według identyfikatora.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obiektu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [Manifest](#manifest) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Tworzenie obrazu

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Opis
Tworzy obraz jako obraz platformy Docker w usłudze Azure Container Registry.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| imageRequest | treść | Ładunek, który jest używany do tworzenia obrazu. | Yes | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Nagłówki | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adres URL lokalizacji operacji asynchronicznej. Wywołanie GET Pokaż stan zadania tworzenia obrazu. | Operacja lokalizacji |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Zapytanie listy obrazów w ramach konta

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Opis
Wysyła zapytanie do listy obrazów w ramach konta. Można filtrować listę wyników według Identyfikatora manifestu i nazwę. Jeśli filtr nie zostanie przekazana, w zapytaniu przedstawiono wszystkie obrazy w ramach konta. Zwrócona lista jest podzielony na strony, a liczba elementów na każdej stronie jest parametrem opcjonalnym.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| manifestId | query | Identyfikatora manifestu. | Nie | ciąg |
| manifestName | query | Nazwa manifestu. | Nie | ciąg |
| count | query | Liczba elementów do pobrania na stronie. | Nie | ciąg |
| $skipToken | query | Token kontynuacji do pobrania następnej strony. | Nie | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [PaginatedImageList](#paginatedimagelist) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Uzyskaj szczegółowe informacje dotyczące obrazu

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Opis
Pobiera obraz według identyfikatora.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obrazu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [Obraz](#image) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Tworzenie usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Opis
Tworzy usługę z obrazu.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| żądania obsługi | treść | Ładunek, który służy do tworzenia usługi. | Yes | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Nagłówki | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adres URL lokalizacji operacji asynchronicznej. Wywołanie GET Pokaż stan zadania tworzenia usługi. | Operacja lokalizacji |
| 409 | Usługa o określonej nazwie już istnieje. |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Zapytanie zawiera listę usług w ramach konta

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Opis
Wysyła zapytanie do listy usług w ramach konta. Można filtrować listę wyników według Identyfikatora nazwy modelu, Identyfikatora/nazwy manifestu, identyfikator obrazu, nazwa usługi lub identyfikatora zasobów obliczeniowych usługi Machine Learning Jeśli filtr nie zostanie przekazana, zapytanie Wyświetla listę wszystkich usług w ramach konta. Zwrócona lista jest podzielony na strony, a liczba elementów na każdej stronie jest parametrem opcjonalnym.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| serviceName | query | Nazwa usługi. | Nie | ciąg |
| modelId | query | Nazwa modelu. | Nie | ciąg |
| modelName | query | Identyfikator modelu. | Nie | ciąg |
| manifestId | query | Identyfikatora manifestu. | Nie | ciąg |
| manifestName | query | Nazwa manifestu. | Nie | ciąg |
| imageId | query | Identyfikator obrazu. | Nie | ciąg |
| computeResourceId | query | Machine Learning obliczeń identyfikator zasobu. | Nie | ciąg |
| count | query | Liczba elementów do pobrania na stronie. | Nie | ciąg |
| $skipToken | query | Token kontynuacji do pobrania następnej strony. | Nie | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [PaginatedServiceList](#paginatedservicelist) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Pobierz szczegóły usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Opis
Pobiera usługę według identyfikatora.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obiektu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [ServiceResponse](#serviceresponse) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Aktualizowanie usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Opis
Aktualizuje istniejącą usługę.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obiektu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| serviceUpdateRequest | treść | Ładunek, który służy do aktualizowania istniejącej usługi. | Yes |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Nagłówki | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adres URL lokalizacji operacji asynchronicznej. Wywołanie GET Pokaż stan zadania aktualizacji usługi. | Operacja lokalizacji |
| 404 | Usługi o określonym identyfikatorze nie istnieje. |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Usuwanie usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Opis
Usuwa usługę.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator obiektu. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. |  |
| 204 | Usługi o określonym identyfikatorze nie istnieje. |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Pobierz klucze usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Opis
Pobiera klucze usługi.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator usługi. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [AuthKeys](#authkeys)
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Wygeneruj ponownie klucze usługi

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| POST |  / Interfejs API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / — regenerateKeys | 

### <a name="description"></a>Opis
Ponownie generuje klucze usługi i zwraca je.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator usługi. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| regenerateKeyRequest | treść | Ładunek, który służy do aktualizowania istniejącej usługi. | Yes | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [AuthKeys](#authkeys)
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Lista wdrożeń w ramach konta kwerendy

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Opis
Wysyła zapytanie do listy wdrożeń w ramach konta. Można filtrować listę wyników według identyfikatorów usługi, która będzie zwracać tylko wdrożenia, które są tworzone dla określonej usługi. Jeśli filtr nie zostanie przekazana, w zapytaniu przedstawiono wszystkie wdrożenia w ramach konta.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |
| serviceId | query | Identyfikator usługi. | Nie | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [DeploymentList](#deploymentlist) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Pobierz szczegóły wdrożenia

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Opis
Pobiera wdrożenia według identyfikatora.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator wdrożenia. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [Wdrożenie](#deployment) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Pobierz szczegóły operacji

### <a name="request"></a>Żądanie
| Metoda | Identyfikator URI żądania |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Opis
Pobiera stan operacji asynchronicznej, według identyfikatora operacji.

### <a name="parameters"></a>Parametry
| Name (Nazwa) | Lokalizacja | Opis | Wymagane | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | ścieżka | Identyfikator subskrypcji platformy Azure. | Yes | ciąg |
| resourceGroupName | ścieżka | Nazwa grupy zasobów, w którym znajduje się konto zarządzania modelami. | Yes | ciąg |
| accountName | ścieżka | Nazwa konta zarządzania modelami. | Yes | ciąg |
| id | ścieżka | Identyfikator operacji. | Yes | ciąg |
| wersja interfejsu API | query | Wersja dostawcy zasobów Microsoft.Machine.Learning interfejsu API do użycia. | Yes | ciąg |
| Autoryzacja | nagłówek | Token autoryzacji. Powinna być podobny do "Bearer XXXXXX." | Yes | ciąg |

### <a name="responses"></a>Odpowiedzi
| Kod | Opis | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Powodzenie. | [OperationStatus](#asyncoperationstatus) |
| default | Odpowiedzi na błąd, w tym artykule opisano, dlaczego operacja nie powiodła się. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definicje

<a name="asset"></a>
### <a name="asset"></a>Element zawartości
Obiekt zasobu, na który będą potrzebne podczas tworzenia obrazu platformy Docker.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**id**  <br>*Opcjonalne*|Identyfikator zasobu.|ciąg|
|**mimeType**  <br>*Opcjonalne*|Typ MIME zawartości modelu. Aby uzyskać więcej informacji na temat typu MIME, zobacz [listę typów nośników IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|ciąg|
|**unpack**  <br>*Opcjonalne*|Wskazuje, której potrzebujemy do rozpakowywania zawartości podczas tworzenia obrazu platformy Docker.|wartość logiczna|
|**adres URL**  <br>*Opcjonalne*|Adres URL lokalizacji zawartości.|ciąg|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Stan operacji asynchronicznej.

*Typ*: wyliczenia (NotStarted uruchomiona, odwołania, zakończone powodzeniem, zakończone niepowodzeniem)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Stan operacji.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**createdTime**  <br>*Opcjonalne*  <br>*read-only*|Czas utworzenia operacji asynchronicznej (UTC).|ciąg (Data godzina)|
|**endTime**  <br>*Opcjonalne*  <br>*read-only*|Czas zakończenia operacji asynchronicznej (UTC).|ciąg (Data godzina)|
|**Błąd**  <br>*Opcjonalne*||[ErrorResponse](#errorresponse)|
|**id**  <br>*Opcjonalne*|Identyfikator operacji asynchronicznej|ciąg|
|**Typ operacji**  <br>*Opcjonalne*|Typ operacji asynchronicznej.|wyliczenia (obraz, usługa)|
|**resourceLocation**  <br>*Opcjonalne*|Zasób utworzone lub zaktualizowane przez operacji asynchronicznej.|ciąg|
|**state**  <br>*Opcjonalne*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Klucze uwierzytelniania dla usługi.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**primaryKey**  <br>*Opcjonalne*|Klucz podstawowy.|ciąg|
|**secondaryKey**  <br>*Opcjonalne*|Klucz pomocniczy.|ciąg|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Ustawienia skalowania automatycznego.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**autoscaleEnabled**  <br>*Opcjonalne*|Włączanie lub wyłączanie skalowania automatycznego.|wartość logiczna|
|**maxReplicas**  <br>*Opcjonalne*|Maksymalna liczba replik zasobników do skalowania.  <br>**Minimalna wartość**: `1`|liczba całkowita|
|**minReplicas**  <br>*Opcjonalne*|Minimalna liczba replik zasobników, aby możliwe było skalowanie w dół.  <br>**Minimalna wartość**: `0`|liczba całkowita|
|**refreshPeriodInSeconds**  <br>*Opcjonalne*|Czas wyzwalacza automatycznego skalowania odświeżenia.  <br>**Minimalna wartość**: `1`|liczba całkowita|
|**targetUtilization**  <br>*Opcjonalne*|Procent użycia, która powoduje uruchomienie skalowania automatycznego.  <br>**Minimalna wartość**: `0`  <br>**Maksymalna wartość**: `100`|liczba całkowita|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Zasobu obliczeniowego usługi Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**id**  <br>*Opcjonalne*|Identyfikator zasobu.|ciąg|
|**type**  <br>*Opcjonalne*|Typ zasobu.|wyliczenia (klastrowy)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfiguracja, aby zarezerwować zasoby dla kontenera w klastrze.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**cpu**  <br>*Opcjonalne*|Określa zastrzeżenie procesora CPU. Format dla rozwiązania Kubernetes: zobacz [znaczenie Procesora](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|ciąg|
|**Pamięć**  <br>*Opcjonalne*|Określa rezerwacją pamięci. Format dla rozwiązania Kubernetes: zobacz [znaczenie pamięci](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|ciąg|


<a name="deployment"></a>
### <a name="deployment"></a>Wdrożenie
Wystąpienie wdrożenia usługi Azure Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**createdAt**  <br>*Opcjonalne*  <br>*read-only*|Godzina utworzenia wdrożenia (UTC).|ciąg (Data godzina)|
|**expiredAt**  <br>*Opcjonalne*  <br>*read-only*|Wygasłe wdrożenia godzina (UTC).|ciąg (Data godzina)|
|**id**  <br>*Opcjonalne*|Identyfikator wdrożenia.|ciąg|
|**imageId**  <br>*Opcjonalne*|Identyfikator obrazu skojarzonego z tym wdrożeniem.|ciąg|
|**serviceName**  <br>*Opcjonalne*|Nazwa usługi.|ciąg|
|**status**  <br>*Opcjonalne*|Bieżący stan wdrożenia.|ciąg|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Tablica obiektów wdrożenia.

*Typ*: <[wdrożenia](#deployment)> tablicy


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Model, szczegóły błędu usługi zarządzania.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Kod**  <br>*Wymagane*|Kod błędu.|ciąg|
|**Komunikat**  <br>*Wymagane*|Komunikat o błędzie.|ciąg|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Obiekt błędu usługi Zarządzanie modelami.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Kod**  <br>*Wymagane*|Kod błędu.|ciąg|
|**Szczegółowe informacje**  <br>*Opcjonalne*|Tablica obiektów szczegółów błędu.|<[ErrorDetail](#errordetail)> tablicy|
|**Komunikat**  <br>*Wymagane*|Komunikat o błędzie.|ciąg|
|**statusCode**  <br>*Opcjonalne*|Kod stanu HTTP.|liczba całkowita|


<a name="image"></a>
### <a name="image"></a>Image (Obraz)
Obraz usługi Azure Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**computeResourceId**  <br>*Opcjonalne*|Identyfikator środowiska utworzone w zasobach obliczeniowych usługi Machine Learning.|ciąg|
|**createdTime**  <br>*Opcjonalne*|Obraz godzina utworzenia (UTC).|ciąg (Data godzina)|
|**creationState**  <br>*Opcjonalne*||[AsyncOperationState](#asyncoperationstate)|
|**Opis elementu**  <br>*Opcjonalne*|Tekst opisu obrazu.|ciąg|
|**Błąd**  <br>*Opcjonalne*||[ErrorResponse](#errorresponse)|
|**id**  <br>*Opcjonalne*|Identyfikator obrazu.|ciąg|
|**imageBuildLogUri**  <br>*Opcjonalne*|Identyfikator URI przekazanych dzienników z budowania obrazu.|ciąg|
|**imageLocation**  <br>*Opcjonalne*|Ciąg lokalizacji usługi Azure Container Registry utworzonemu obrazowi.|ciąg|
|**imageType**  <br>*Opcjonalne*||[ImageType](#imagetype)|
|**manifest**  <br>*Opcjonalne*||[Manifest](#manifest)|
|**Nazwa**  <br>*Opcjonalne*|Nazwa obrazu.|ciąg|
|**Wersja**  <br>*Opcjonalne*|Wersja obrazu ustawiane przez usługę Zarządzanie modelami.|liczba całkowita|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Żądanie utworzenia obrazu usługi Azure Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**computeResourceId**  <br>*Wymagane*|Identyfikator środowiska utworzone w zasobach obliczeniowych usługi Machine Learning.|ciąg|
|**Opis elementu**  <br>*Opcjonalne*|Tekst opisu obrazu.|ciąg|
|**imageType**  <br>*Wymagane*||[ImageType](#imagetype)|
|**manifestId**  <br>*Wymagane*|Identyfikator manifestu, z której zostanie utworzony obraz.|ciąg|
|**Nazwa**  <br>*Wymagane*|Nazwa obrazu.|ciąg|


<a name="imagetype"></a>
### <a name="imagetype"></a>Typ obrazu
Określa typ obrazu.

*Typ*: wyliczenia (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Manifest usługi Azure Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Zasoby**  <br>*Wymagane*|Lista zasobów.|<[Zasób](#asset)> tablicy|
|**createdTime**  <br>*Opcjonalne*  <br>*read-only*|Czas manifestu utworzenia (UTC).|ciąg (Data godzina)|
|**Opis elementu**  <br>*Opcjonalne*|Manifest tekst opisu.|ciąg|
|**driverProgram**  <br>*Wymagane*|Sterownik programu manifestu.|ciąg|
|**id**  <br>*Opcjonalne*|Identyfikatora manifestu.|ciąg|
|**modelIds**  <br>*Opcjonalne*|Lista identyfikatorów modelu zarejestrowane modele. Żądanie zakończy się niepowodzeniem, jeśli dowolny uwzględnione modele nie są zarejestrowane.|<string> Tablica|
|**modelType**  <br>*Opcjonalne*|Określa, że modele są już zarejestrowane w usłudze Zarządzanie modelami w usłudze.|wyliczenia (Registered)|
|**Nazwa**  <br>*Wymagane*|Nazwa manifestu.|ciąg|
|**targetRuntime**  <br>*Wymagane*||[TargetRuntime](#targetruntime)|
|**Wersja**  <br>*Opcjonalne*  <br>*read-only*|Wersja manifestu przypisany przez usługę Zarządzanie modelami.|liczba całkowita|
|**webserviceType**  <br>*Opcjonalne*|Określa typ żądanej usługi sieci web, która zostanie utworzona z manifestu.|wyliczenia (w czasie rzeczywistym)|


<a name="model"></a>
### <a name="model"></a>Modelowanie
Wystąpienie modelu usługi Azure Machine Learning.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**createdAt**  <br>*Opcjonalne*  <br>*read-only*|Model godzina utworzenia (UTC).|ciąg (Data godzina)|
|**Opis elementu**  <br>*Opcjonalne*|Tekst opisu modelu.|ciąg|
|**id**  <br>*Opcjonalne*  <br>*read-only*|Identyfikator modelu.|ciąg|
|**mimeType**  <br>*Wymagane*|Typ MIME zawartości modelu. Aby uzyskać więcej informacji na temat typu MIME, zobacz [listę typów nośników IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|ciąg|
|**Nazwa**  <br>*Wymagane*|Nazwa modelu.|ciąg|
|**tagów**  <br>*Opcjonalne*|Lista znaczników modelu.|<string> Tablica|
|**unpack**  <br>*Opcjonalne*|Wskazuje, czy musimy rozpakować modelu podczas tworzenia obrazu platformy Docker.|wartość logiczna|
|**adres URL**  <br>*Wymagane*|Adres URL modelu. Zazwyczaj umieściliśmy adresu URL sygnatury dostępu współdzielonego w tym miejscu.|ciąg|
|**Wersja**  <br>*Opcjonalne*  <br>*read-only*|Wersja modelu przypisany przez usługę Zarządzanie modelami.|liczba całkowita|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informacje o kolekcji danych modelu.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**eventHubEnabled**  <br>*Opcjonalne*|Włącz Centrum zdarzeń dla usługi.|wartość logiczna|
|**storageEnabled**  <br>*Opcjonalne*|Włącz magazyn dla usługi.|wartość logiczna|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Z podziałem na strony listy obrazów.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**nextLink**  <br>*Opcjonalne*|Kontynuacji (bezwzględny identyfikator URI linku) do następnej strony wyników na liście.|ciąg|
|**value**  <br>*Opcjonalne*|Tablica obiektów modelu.|<[Obraz](#image)> tablicy|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Lista z podziałem na strony manifestów.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**nextLink**  <br>*Opcjonalne*|Kontynuacji (bezwzględny identyfikator URI linku) do następnej strony wyników na liście.|ciąg|
|**value**  <br>*Opcjonalne*|Tablica obiektów manifestu.|<[Manifest](#manifest)> tablicy|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Z podziałem na strony listy modeli.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**nextLink**  <br>*Opcjonalne*|Kontynuacji (bezwzględny identyfikator URI linku) do następnej strony wyników na liście.|ciąg|
|**value**  <br>*Opcjonalne*|Tablica obiektów modelu.|<[Model](#model)> tablicy|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Z podziałem na strony Lista usług.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**nextLink**  <br>*Opcjonalne*|Kontynuacji (bezwzględny identyfikator URI linku) do następnej strony wyników na liście.|ciąg|
|**value**  <br>*Opcjonalne*|Tablica obiektów usługi.|<[ServiceResponse](#serviceresponse)> tablicy|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Żądanie, aby utworzyć usługę.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**appInsightsEnabled**  <br>*Opcjonalne*|Włącz usługę application insights dla usługi.|wartość logiczna|
|**autoScaler**  <br>*Opcjonalne*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*Wymagane*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Opcjonalne*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Opcjonalne*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Wymagane*|Obraz, aby utworzyć usługę.|ciąg|
|**maxConcurrentRequestsPerContainer**  <br>*Opcjonalne*|Maksymalna liczba jednoczesnych żądań.  <br>**Minimalna wartość**: `1`|liczba całkowita|
|**Nazwa**  <br>*Wymagane*|Nazwa usługi.|ciąg|
|**numReplicas**  <br>*Opcjonalne*|Liczba replik zasobników uruchamianie w dowolnym momencie. Nie można określić, czy włączono skalowania automatycznego.  <br>**Minimalna wartość**: `0`|liczba całkowita|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Żądanie ponownego generowania klucza dla usługi.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Właściwość KeyType**  <br>*Opcjonalne*|Określa, którego klucza należy ponownie wygenerować.|wyliczenia (podstawowego, pomocniczego)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Szczegółowy stan usługi.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**createdAt**  <br>*Opcjonalne*|Godzina utworzenia usług (UTC).|ciąg (Data godzina)|
|**Identyfikator**  <br>*Opcjonalne*|Identyfikator usługi.|ciąg|
|**image**  <br>*Opcjonalne*||[Obraz](#image)|
|**manifest**  <br>*Opcjonalne*||[Manifest](#manifest)|
|**Modele**  <br>*Opcjonalne*|Lista modeli.|<[Model](#model)> tablicy|
|**Nazwa**  <br>*Opcjonalne*|Nazwa usługi.|ciąg|
|**scoringUri**  <br>*Opcjonalne*|Identyfikator URI do oceniania usługi.|ciąg|
|**state**  <br>*Opcjonalne*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*Opcjonalne*|Data ostatniej aktualizacji (UTC).|ciąg (Data godzina)|
|**appInsightsEnabled**  <br>*Opcjonalne*|Włącz usługę application insights dla usługi.|wartość logiczna|
|**autoScaler**  <br>*Opcjonalne*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*Wymagane*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Opcjonalne*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Opcjonalne*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*Opcjonalne*|Maksymalna liczba jednoczesnych żądań.  <br>**Minimalna wartość**: `1`|liczba całkowita|
|**numReplicas**  <br>*Opcjonalne*|Liczba replik zasobników uruchamianie w dowolnym momencie. Nie można określić, czy włączono skalowania automatycznego.  <br>**Minimalna wartość**: `0`|liczba całkowita|
|**Błąd**  <br>*Opcjonalne*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Żądanie, aby zaktualizować usługę.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**appInsightsEnabled**  <br>*Opcjonalne*|Włącz usługę application insights dla usługi.|wartość logiczna|
|**autoScaler**  <br>*Opcjonalne*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*Opcjonalne*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Opcjonalne*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Opcjonalne*|Obraz, aby utworzyć usługę.|ciąg|
|**maxConcurrentRequestsPerContainer**  <br>*Opcjonalne*|Maksymalna liczba jednoczesnych żądań.  <br>**Minimalna wartość**: `1`|liczba całkowita|
|**numReplicas**  <br>*Opcjonalne*|Liczba replik zasobników uruchamianie w dowolnym momencie. Nie można określić, czy włączono skalowania automatycznego.  <br>**Minimalna wartość**: `0`|liczba całkowita|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Typ docelowe środowisko uruchomieniowe.


|Name (Nazwa)|Opis|Schemat|
|---|---|---|
|**Właściwości**  <br>*Wymagane*||< string, string > mapy|
|**runtimeType**  <br>*Wymagane*|Określa środowisko wykonawcze.|wyliczenia (SparkPython, Python)|

