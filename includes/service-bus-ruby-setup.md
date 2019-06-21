---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183464"
---
## <a name="create-a-ruby-application"></a>Tworzenie aplikacji w języku Ruby
Aby uzyskać instrukcje, zobacz [tworzenie aplikacji Ruby na platformie Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Aby korzystać z usługi Service Bus, Pobierz i za pomocą pakietu Azure Ruby, który zawiera zbiór bibliotek wygody, które komunikują się z usług REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "azure gem instalacji" w oknie wiersza polecenia, aby zainstalować rozwiązanie gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Za pomocą ulubionego edytora tekstu, Dodaj następujący element do górnej części pliku języka Ruby, w którym zamierzasz korzystać z magazynu:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Aby ustawić wartości przestrzeni nazw, nazwę klucza, klucz podpisujący i hosta, użyj następującego kodu:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ustaw wartość przestrzeni nazw do wartości utworzonej zamiast pełnego adresu URL. Na przykład użyć **"yourexamplenamespace"** , nie "yourexamplenamespace.servicebus.windows.net".
