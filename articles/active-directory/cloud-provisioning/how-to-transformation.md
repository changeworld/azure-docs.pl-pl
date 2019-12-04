---
title: Azure AD Connect przekształcenia aprowizacji w chmurze
description: W tym dokumencie opisano sposób użycia transformacji w celu zmiany domyślnych mapowań atrybutów.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794473"
---
# <a name="transformations"></a>Przekształcenia

Przekształcenie pozwala zmienić domyślne zachowanie tego, jak atrybut jest synchronizowany z usługą Azure AD przy użyciu aprowizacji w chmurze.  

Aby wykonać to zadanie, należy edytować schemat, a następnie przesłać go ponownie za pośrednictwem żądania sieci Web.

Aby uzyskać więcej informacji na temat atrybutów aprowizacji w chmurze, zobacz [Omówienie schematu usługi Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Pobierz schemat
Aby pobrać schemat, należy wykonać czynności opisane w temacie [wyświetlanie schematu](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Mapowanie atrybutów niestandardowych
Aby dodać niestandardowe Mapowanie atrybutów, należy wykonać poniższą procedurę.

1. Skopiuj schemat do tekstu lub edytora kodu, takiego jak [Visual Studio Code](https://code.visualstudio.com/).  
2. Zlokalizuj obiekt, który ma zostać zaktualizowany w schemacie ![](media/how-to-transformation/transform1.png)</br>
3. Znajdź kod dla **ExtensionAttribute3** w obiekcie User.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  Edytuj kod, tak aby atrybut Company został zamapowany na ExtensionAttribute3.
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. Skopiuj schemat z powrotem do Eksploratora grafów, a następnie zmień typ żądania, aby umieścić i **uruchomić zapytanie**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Teraz w Azure Portal przejdź do konfiguracji aprowizacji w chmurze i **ponownie rozpocznij Inicjowanie obsługi administracyjnej**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Za chwilę Sprawdź, czy atrybuty są wypełniane, uruchamiając następujące zapytanie w Eksploratorze grafu: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Powinna zostać wyświetlona wartość.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Mapowanie atrybutów niestandardowych za pomocą funkcji
Aby uzyskać bardziej zaawansowane mapowanie, można użyć funkcji, które umożliwią manipulowanie danymi i tworzenie wartości dla atrybutu zgodnie z potrzebami organizacji.

Aby wykonać to zadanie, po prostu wykonaj powyższe kroki, a następnie Edytuj funkcję, która jest używana do konstruowania wartości końcowej.

Aby uzyskać informacje na temat składni i Przykłady wyrażeń, zobacz [Pisanie wyrażeń pod kątem mapowań atrybutów w Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
