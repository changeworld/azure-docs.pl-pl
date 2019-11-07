---
title: Użyj Azure Key Vault wpisów tajnych w działaniach potoku
description: Dowiedz się, jak pobrać przechowywane poświadczenia z magazynu kluczy Azure i korzystać z nich podczas uruchamiania potoków usługi Data Factory.
services: data-factory
author: ChrisLound
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 83d0981a0d277eab1aae8654343ab34661b3e88b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672894"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Użyj Azure Key Vault wpisów tajnych w działaniach potoku

Poświadczenia lub wartości tajne można przechowywać w Azure Key Vault i używać ich podczas wykonywania potoku w celu przekazania do działań.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanej przez fabrykę danych.  Dowiedz się, jak działa z [tożsamości zarządzanej dla Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) i upewnij się, że Fabryka danych ma już skojarzoną.

## <a name="steps"></a>Kroki

1. Otwórz właściwości fabryki danych i skopiuj wartość identyfikatora aplikacji zarządzanej tożsamości.

    ![Wartość tożsamości zarządzanej](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otwórz zasady dostępu do magazynu kluczy i Dodaj uprawnienia zarządzanej tożsamości, aby uzyskać i wyświetlić wpisy tajne.

    ![Zasady dostępu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zasady dostępu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kliknij przycisk **Dodaj**, a następnie kliknij przycisk **Zapisz**.

3. Przejdź do wpisu tajnego Key Vault i skopiuj identyfikator wpisu tajnego.

    ![Identyfikator wpisu tajnego](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Zanotuj swój tajny identyfikator URI, który chcesz uzyskać podczas przebiegu potoku fabryki danych.

4. W potoku Data Factory Dodaj nowe działanie sieci Web i skonfiguruj je w następujący sposób.  

    |Właściwość  |Wartość  |
    |---------|---------|
    |Bezpieczne wyjście     |True         |
    |Adres URL     |[Tajna wartość identyfikatora URI]? API-version = 7.0         |
    |Metoda     |GET         |
    |Authentication     |Tożsamość usługi zarządzanej         |
    |Zasób        |https://vault.azure.net       |

    ![Aktywność sieci Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Musisz dodać **? API-version = 7.0** na końcu TAJNEGO identyfikatora URI.  

    > [!CAUTION]
    > Ustaw opcję bezpieczne wyjście na wartość true, aby uniemożliwić Logowanie wartości klucza tajnego w postaci zwykłego tekstu.  Wszelkie dalsze działania, które zużywają tę wartość, powinny mieć ustawioną opcję bezpiecznego wprowadzania wartość true.

5. Aby użyć wartości w innym działaniu, użyj następującego wyrażenia kodu **@activity("Web"). Output. Value)** .

    ![Wyrażenie kodu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać Azure Key Vault do przechowywania poświadczeń dla magazynów danych i obliczeń, zobacz temat [przechowywanie poświadczeń w programie Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
