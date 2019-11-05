---
title: 'PowerShell: Włączanie BYOK TDE-Azure SQL Database wystąpienia zarządzanego | Microsoft Docs'
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL, aby rozpocząć korzystanie z BYOK Transparent Data Encryption (TDE) na potrzeby szyfrowania w środowisku REST przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: 0413216bc666aff504193d6723d46a6ee26be8ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500057"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Zarządzaj Transparent Data Encryption w zarządzanym wystąpieniu przy użyciu własnego klucza z Azure Key Vault

Ten przykład skryptu programu PowerShell służy do konfigurowania Transparent Data Encryption (TDE) w scenariuszu Bring Your Own Key (wersja zapoznawcza) dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault. Aby dowiedzieć się więcej na temat obsługi TDE z Bring Your Own Key (BYOK), zobacz [TDE Bring Your Own Key do usługi Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące wystąpienie zarządzane. Zobacz [Używanie programu PowerShell do tworzenia Azure SQL Database wystąpienia zarządzanego](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Korzystanie z programu PowerShell lokalnie lub przy użyciu Azure Cloud Shell wymaga AZ PowerShell 1.1.1-Preview lub nowszej wersji zapoznawczej. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)lub uruchamianie poniższego przykładowego skryptu w celu zainstalowania modułu.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
