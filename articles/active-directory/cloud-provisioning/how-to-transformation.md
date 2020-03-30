---
title: Transformacje inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect
description: W tym artykule opisano sposób używania przekształceń do zmiany domyślnych mapowań atrybutów.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549299"
---
# <a name="transformations"></a>Przekształcenia

Dzięki transformacji można zmienić domyślne zachowanie sposobu synchronizacji atrybutu z usługą Azure Active Directory (Azure AD) przy użyciu inicjowania obsługi administracyjnej w chmurze.

Aby wykonać to zadanie, należy edytować schemat, a następnie ponownie przesłać go za pośrednictwem żądania sieci web.

Aby uzyskać więcej informacji na temat atrybutów inicjowania obsługi administracyjnej w chmurze, zobacz [Opis schematu usługi Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Pobieranie schematu
Aby pobrać schemat, wykonaj kroki opisane w [widoku schematu](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Niestandardowe mapowanie atrybutów
Aby dodać niestandardowe mapowanie atrybutów, wykonaj następujące kroki.

1. Skopiuj schemat do edytora tekstu lub kodu, takiego jak [Visual Studio Code](https://code.visualstudio.com/).
1. Zlokalizuj obiekt, który chcesz zaktualizować w schemacie.

   ![Obiekt w schemacie](media/how-to-transformation/transform1.png)</br>
1. Znajdź kod `ExtensionAttribute3` w obiekcie użytkownika.

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
1. Edytuj kod tak, aby atrybut firmy `ExtensionAttribute3`był mapowany na .

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
 1. Skopiuj schemat z powrotem do Eksploratora wykresu, zmień **typ żądania** na **PUT**i wybierz polecenie **Uruchom kwerendę**.

    ![Uruchamianie zapytania](media/how-to-transformation/transform2.png)

 1. Teraz w witrynie Azure portal przejdź do konfiguracji inicjowania obsługi administracyjnej w chmurze i wybierz pozycję **Uruchom ponownie aprowizacji.**

    ![Ponowne uruchamianie obsługi administracyjnej](media/how-to-transformation/transform3.png)

 1. Po chwili sprawdź, czy atrybuty są wypełniane, uruchamiając `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`następującą kwerendę w Eksploratorze wykresu: .
 1. Teraz powinna zostać wyświetlna wartość.

    ![Pojawia się wartość](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Niestandardowe mapowanie atrybutów z funkcją
W przypadku bardziej zaawansowanego mapowania można użyć funkcji, które umożliwiają manipulowanie danymi i tworzenie wartości atrybutów dostosowanych do potrzeb organizacji.

Aby wykonać to zadanie, wykonaj poprzednie kroki, a następnie edytuj funkcję, która jest używana do konstruowania wartości końcowej.

Aby uzyskać informacje na temat składni i przykładów wyrażeń, zobacz [Pisanie wyrażeń dla mapowań atrybutów w usłudze Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
