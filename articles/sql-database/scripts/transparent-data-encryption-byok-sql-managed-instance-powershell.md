---
title: 'PowerShell: Włącz byok TDE — wystąpienie zarządzanej bazy danych SQL platformy Azure '
description: Dowiedz się, jak skonfigurować wystąpienie zarządzanego sql platformy Azure, aby rozpocząć korzystanie z szyfrowania danych (TDE) byok dla szyfrowania w spoczynku przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691397"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Zarządzanie funkcją Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault

Ten przykład skryptu programu PowerShell konfiguruje przezroczyste szyfrowanie danych (TDE) przy użyciu klucza zarządzanego przez klienta dla wystąpienia zarządzanego sql platformy Azure przy użyciu klucza z usługi Azure Key Vault. Jest to często określane jako Bring Your Own Key scenariusz TDE. Aby dowiedzieć się więcej o TDE z kluczem zarządzanym przez klienta, zobacz [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące wystąpienie zarządzane. Zobacz [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database za pomocą programu PowerShell.](sql-database-create-configure-managed-instance-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Przy użyciu programu PowerShell lokalnie lub przy użyciu usługi Azure Cloud Shell wymaga AZ PowerShell 2.3.2 lub nowszej wersji. Jeśli chcesz przeprowadzić uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps)lub uruchom poniższy przykładowy skrypt, aby zainstalować moduł dla bieżącego użytkownika:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
