# WavesJ (Java Library)

RU | [EN](GOOGLE.RU)

## Описание библиотеки ##
Библиотека, написанная на Java, позволяющая взаимодействовать с блокчейном Waves.<br>
Она предоставляет собой набор готовых инструментов, позволяющих работать c:
- Аккаунтами;
- Транзакциями;
- Нодой Waves.

## Содержание ##
  - [Основные возможности](#основные-возможности)
  - [Начало работы](#начало-работы)
    - [Требования](#требования)
    - [Установка](#установка)
  - [Использование](#использование)
    - [Работа с аккаунтом](#работа-с-аккаунтом)
    - [Работа с транзакциями](#работа-с-транзакциями)
    - [Работа с нодой](#работа-с-нодой)

## Основные возможности ##
1. Работа с аккаунтами:
   - Создание аккаунта с публичным/приватным ключём;
   - Создание адреса кошелька.
2. Работа с транзакциями:
   - Подпись и отправка транзакций на ноду;
   - Установка скриптов на аккаунт/ассет;
   - Подпись биржевых ордеров и отправка их на матчер;
   - Создание и сжигание токенов;
   - Лизинг и спонсирование.
3. Взаимодействие с нодой Waves.
   - To be done
   
## Начало работы ##
### Требования ###
```
Версия JDK 1.8+
```
###  Установка ###
Для добавления библиотеки WavesJ в ваш проект возможны следующие вариации.

Maven:
```
<dependency>
    <groupId>com.wavesplatform</groupId>
    <artifactId>wavesj</artifactId>
    <version>1.4.1</version>
</dependency>
```
[Ссылка на библиотеку на портале Maven Central.](https://search.maven.org/artifact/com.wavesplatform/wavesj)

Gradle:
```
compile group: 'com.wavesplatform', name: 'wavesj', version: '1.4.1'
```

SBT:
```
libraryDependencies += "com.wavesplatform" % "wavesj" % "1.4.1"
```
## Использование ##
### Работа с аккаунтом ###
В качестве примера работы с аккаунтом рассмотрим:
- Генерацию seed-фразы;
- Создание приватного и публичных ключей из seed-фразы;
- Генерацию адреса кошелька из публичного ключа. 

Импортируем все необходимые зависимости:
```Java
package node;
import com.wavesplatform.crypto.Crypto;
import com.wavesplatform.transactions.account.PrivateKey;
import com.wavesplatform.transactions.account.PublicKey;
import com.wavesplatform.transactions.account.Address;
import com.wavesplatform.transactions.common.ChainId;
```
В следующей последовательности мы:
1. Генерируем случайный seed;
2. Создаем приватный ключ из seed для пользователя alice;
3. Создаем публичный ключ из приватного ключа;
4. Формируем адрес кошелька из публичного ключа в сети STAGENET.

```Java
String seed = Crypto.getRandomSeedPhrase();
PrivateKey alice = PrivateKey.fromSeed(seed);
PublicKey publicKey = PublicKey.from(alice);
Address address = Address.from(ChainId.STAGENET, alice.publicKey());
```
### Работа с транзакциями ###
**Список транзакций**<br>
На платформе Waves возможно проведение следующих типов транзакций:
   1. Токенизация:
       - [Транзакция выпуска](#issue-transaction)
       - [Транзакция довыпуска](#reissue-transaction)
       - [Транзакция установки скрипта ассета](#set-asset-script-transaction)
       - [Транзакция обновления информации ассета](#update-asset-info-transaction)
       - [Транзакция сжигания токена](#burn-transaction)
   2. Использование
       - [Транзакция перевода](#transfer-transaction)
       - [Транзакция обмена](#exchange-transaction)
       - [Транзакция массового перевода](#mass-transfer-transaction)
       - [Транзакция установки скрипта](#set-script-transaction)
       - [Транзакция вызова скрипта](#invoke-script-transaction)
       - [Транзакция данных](#data-transaction)
       - [Транзакция создания псевдонима](#create-alias-transaction)
       - [Транзакция в формате Ethereum]()
   3. Сеть
       - [Транзакция лизинга](#lease-transaction)
       - [Транзакция закрытия лизинга](#lease-cancel-transaction)
       - [Транзакция спонсирования](#sponsor-fee-transaction)

<br>
 
**Начало работы с транзакциями**<br>
Для начала работы с транзакиями, необходимо создать клиент для сети блокчейна.<br>
Выбираем одну из сетей: TESTNET, STAGENET или MAINNET.
```Java
import com.wavesplatform.wavesj.Node;
import com.wavesplatform.wavesj.Profile;
Node node = new Node(Profile.TESTNET);
```
Подробнее о [Сетях блокчейна: Mainnet, Testnet, Stagenet](https://docs.waves.tech/ru/blockchain/blockchain-network/)<br>
Генерация бесплатных токенов в тестовой сети через [Faucet](https://testnet.wavesexplorer.com/faucet).

<br>Учитывая особенности языка Java, возможны два способа реализации функций: через **builder** или **constructor**.<br>
Для каждого из методов транзакций ниже будут продемонстрированы варианты написания двумя способами.<br><br>

---

**Токенизация:**
<!-- IssueTransaction = Транзакция выпуска -->
#### `Issue Transaction` ####
Транзакция выпуска создает новый [токен](https://docs.waves.tech/ru/blockchain/token/).<br>
Подробнее про [транзакцию выпуска](https://docs.waves.tech/ru/blockchain/transaction-type/issue-transaction).

Создадим ассет с наименованием AssetName в количестве 1000 штук и двумя знаками после запятой.<br>
Далее подпишем транзакцию приватным ключем пользователя [alice](#работа-с-аккаунтом).

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;

// Реализация через Builder
IssueTransaction tx = IssueTransaction.builder("AssetName", 1000, 2)
.description("description").script(null).isReiussable(false)
.getSignedWith(alice);

// Реализация через Constructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"AssetName","description",1000,2,false,null)
.addProof(alice); 

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
IssueTransactionInfo txInfo = node.getTransactionInfo(tx.id(), IssueTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("name:" + txInfo.tx().name());
System.out.println("quantity:" + txInfo.tx().quantity());
System.out.println("reissuable:" + txInfo.tx().reissuable());
System.out.println("decimals:" + txInfo.tx().decimals());
System.out.println("description:" + txInfo.tx().description());
System.out.println("script:" + txInfo.tx().script().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| name <span style="color:red">(required)</span> | Название токена. <br>От 4 до 16 байт (1 символ может занимать до 4 байт).| AssetName |
| quantity <span style="color:red">(required)</span> | Количество токена.<br>Целое число, выраженное в минимальных неделимых единицах токена.<br> То есть фактическое количество, умноженное на 10decimals.<br>От 1 до 9 223 372 036 854 775 807.1 для NFT.| 1000 |
| decimals <span style="color:red">(required)</span> | Количество знаков после запятой, от 0 до 8. <br>0 для NFT.| 2 |
| description | Описание токена. От 0 до 1000 байт.| Description|
| reissuable | Флаг возможности довыпуска, см.<br>[Транзакция довыпуска](https://docs.waves.tech/ru/blockchain/transaction-type/reissue-transaction). false для NFT | false |
| script | Для смарт-ассета — скомпилированный скрипт ассета, до 8192 байт, в кодировке base64.<br>При значении null — выпускается токен без скрипта.<br>Если при выпуске к ассету не прикреплен скрипт, в дальнейшем его нельзя сделать смарт-ассетом.| null |
<br>

<!-- Reissue transaction = Транзакция довыпуска -->
#### `Reissue transaction` ####
Транзакция довыпуска увеличивает количество [токена](https://docs.waves.tech/ru/blockchain/token/) на блокчейне и/или запрещает его довыпуск.<br>
Подробнее про [транзакцию довыпуска](https://docs.waves.tech/ru/blockchain/transaction-type/reissue-transaction#%D0%BA%D0%BE%D0%BC%D0%B8%D1%81%D1%81%D0%B8%D1%8F-%D0%B7%D0%B0-%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8E).

Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Далее создадим функцию довыпуска токена, ссылаясь на его assetId.

```Java
// Необходимые импортирования
import com.wavesplatform.wavesj.Node;
import com.wavesplatform.transactions.common.Amount;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.ChainId;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.ReissueTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.wavesj.info.ReissueTransactionInfo;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();
ReissueTransaction tx = ReissueTransaction.builder(Amount.of(1000, assetId)).getSignedWith(alice);


// Реализация через constructor
IssueTransaction itx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true,Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(itx).id(), IssueTransactionInfo.class).itx().assetId();
ReissueTransaction tx = new ReissueTransaction(alice.publicKey(),Amount.of(1000, assetId),true)
        .addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней
node.waitForTransaction(node.broadcast(tx).id());
ReissueTransactionInfo txInfo = node.getTransactionInfo(tx.id(), ReissueTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("quantity:" + txInfo.tx().amount().value());
System.out.println("reissuable:" + txInfo.tx().reissuable());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58.| GSFk5Ziwx33g8KuMyh6wYerxJcHXdcGgXFiBYXH58AE6 |
| quantity <span style="color:red">(required)</span> | Количество токена к довыпуску.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена.<br>Итоговое количество токена в результате довыпуска не должно превышать<br> 9 223 372 036 854 775 807| 1000|
| reissuable <span style="color:red">(required)</span> | Флаг возможности довыпуска. | true |
<br>

<!-- Burn transaction = Транзакция сжигания токена -->
#### `Burn transaction` ####
Транзакция сжигания токена уменьшает количество [токена](https://docs.waves.tech/ru/blockchain/token/) на аккаунте отправителя.<br>
Также, количество токена уменьшается на данную величину на блокчейне.<br>
Подробнее о [транзакции сжигания токена](https://docs.waves.tech/ru/blockchain/transaction-type/burn-transaction).


Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Произведем сжигание токена, ссылаясь на его assetId.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Amount;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.wavesj.info.BurnTransactionInfo;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.transactions.BurnTransaction;
import com.wavesplatform.transactions.IssueTransaction;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(IssueTransaction.builder("Asset", 1000, 2)
        .getSignedWith(alice)).id(), IssueTransactionInfo.class).tx().assetId();
BurnTransaction tx = BurnTransaction.builder(Amount.of(100, assetId)).getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
BurnTransaction tx = new BurnTransaction(alice.publicKey(),new Amount(100, assetId)).addProof(alice);

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
BurnTransactionInfo txInfo = node.getTransactionInfo(tx.id(), BurnTransactionInfo.class);
System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("amount:" + txInfo.tx().amount().value());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());      

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| amount <span style="color:red">(required)</span> | Количество сжигаемого токена.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена. | 100 |
| assetId <span style="color:red">(required)</span> | ID сжигаемого токена в кодировке base58 | GSFk5Ziwx33g8KuMyh6wYerxJcHXdcGgXFiBYXH58AE6 
<br>

<!-- Set asset script = Транзакция установки скрипта ассета -->
#### `Set asset script transaction` ####
Транзакция установки скрипта ассета изменяет скрипт ассета.<br> 
Подробнее о [транзакции установки скрипта ассета]().

Трансформируем написанный скрипт в кодировку base64.<br>
Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Установим скрипт в кодировке base64 на токен.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.SetAssetScriptTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;
import com.wavesplatform.wavesj.info.SetAssetScriptTransactionInfo;

// Реализация через builder
Base64String script = node.compileScript("{-# SCRIPT_TYPE ASSET #-} true").script();
AssetId assetId = node.waitForTransaction(node.broadcast(IssueTransaction.builder("Asset", 1000, 2)
        .script(script).getSignedWith(alice)).id(), IssueTransactionInfo.class).tx().assetId();
SetAssetScriptTransaction tx = SetAssetScriptTransaction.builder(assetId, script).getSignedWith(alice);


// Реализация через сonstructor
Base64String script = node.compileScript("{-# SCRIPT_TYPE ASSET #-} true").script();
IssueTransaction tx = new IssueTransaction(
    alice.publicKey(),"Asset","",1000,2,true,Base64String.empty())
    .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
SetAssetScriptTransaction tx = new SetAssetScriptTransaction(alice.publicKey(), assetId, script).addProof(alice);

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
SetAssetScriptTransactionInfo txInfo = node.getTransactionInfo(tx.id(), SetAssetScriptTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("script:" + txInfo.tx().script().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58. | 7qJUQFxniMQx45wk12UdZwknEW9cDgvfoHuAvwDNVjYv |
| script <span style="color:red">(required)</span> | Cкомпилированный скрипт ассета, до 8192 байт, в кодировке base64. | base64:AQa3b8tH |

<br>

<!-- Update asset info transaction = Транзакция обновления информации ассета -->
#### `Update asset info transaction` ####
Транзакция обновления информации ассета изменяет название и описание [токена](https://docs.waves.tech/ru/blockchain/token/).<br>
Подробнее о [транзакции обновления информации ассета](https://docs.waves.tech/ru/blockchain/transaction-type/update-asset-info-transaction).


Запустим транзакцию создания [тестового токена](#issue-transaction).
Подождем добавления 2 новых блоков блокчейна.
Проведем транзакцию обновления информации ассета.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.UpdateAssetInfoTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();
node.waitBlocks(2);
UpdateAssetInfoTransaction tx = UpdateAssetInfoTransaction
        .builder(assetId, "New Asset", "New description").getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
node.waitBlocks(2);
        
UpdateAssetInfoTransaction tx = new UpdateAssetInfoTransaction(alice.publicKey(),assetId,"New Asset","New description")
    .addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней
node.waitForTransaction(node.broadcast(tx).id());
System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("name:" + txInfo.tx().name());
System.out.println("description:" + txInfo.tx().description());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| name <span style="color:red">(required)</span> | Название токена. От 4 до 16 байт. | New asset name |
| description <span style="color:red">(required)</span> | Описание токена. От 0 до 1000 байт. | Random phrase |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58. | syXBywr2HVY7wxqkaci1jKY73KMpoLh46cp1peJAZNJ |

<br>

---

**Использование** <br>
<!-- Transfer transaction = Транзакция перевода -->
#### `Transfer transaction` ####
Транзакция перевода выполняет перевод указанного количества [токена](https://docs.waves.tech/ru/blockchain/token/) на другой аккаунт.<br>
Подробнее о [транзакции перевода](https://docs.waves.tech/ru/blockchain/transaction-type/transfer-transaction).

Задаем [адрес получателя](#работа-с-аккаунтом) (переменная bob) и ключевые данные транзакции (ID ассета, сумма).<br>
Транзакция подписывается приватным ключём пользователя alice.

```Java
// Необходимые импортирования
import com.wavesplatform.wavesj.info.TransferTransactionInfo;
import com.wavesplatform.transactions.TransferTransaction;
import com.wavesplatform.transactions.common.Amount;

// Реализация через builder
TransferTransaction tx = TransferTransaction.builder(bob.address(), Amount.of(1000)).getSignedWith(alice);

// Реализация через сonstructor
TransferTransaction tx = new TransferTransaction(alice.publicKey(), bob.address(),Amount.of(1000),null).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());
TransferTransactionInfo txInfo = node.getTransactionInfo(tx.id(), TransferTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("recipient:" + txInfo.tx().recipient());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("amount:" + txInfo.tx().amount().value());
System.out.println("attachment:" + txInfo.tx().attachment().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId | ID переводимого токена в кодировке base58.<br>null — перевод в WAVES. | 7uncmN7dZfV3fYVvNdYTngrrbamPYMgwpDnYG1bGy6nA |
| amount <span style="color:red">(required)| Количество токена к переводу.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена.| 1000 |
| recipient <span style="color:red">(required)| Адрес получателя в кодировке base58 или [псевдоним](https://docs.waves.tech/ru/blockchain/account/alias) адреса c префиксом alias:<байт_сети>:.<br>Например alias:T:merry (см.[Байт сети)](https://docs.waves.tech/ru/blockchain/blockchain-network/#%D0%B1%D0%B0%D0%B9%D1%82-%D1%81%D0%B5%D1%82%D0%B8) | 3PFmoN5YLoPNsL4cmNGkRxbUKrUVntwyAhf |
| attachment | 	Произвольные данные (обычно комментарий к транзакции), до 140 байт, в кодировке base58 | 3vrgtyozxuY88J9RqMBBAci2UzAq9DBMFTpMWLPzMygGeSWnD7k | 

<br>

<!-- Set script transaction =  Транзакция установки скрипта -->
#### `Set script transaction` ####
Транзакция установки скрипта прикрепляет [dApp-скрипт](https://docs.waves.tech/ru/ride/script/script-types/dapp-script) или [скрипт аккаунта](https://docs.waves.tech/ru/ride/script/script-types/account-script) к аккаунту отправителя.<br>
Подробнее о [транзакции установки скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/set-script-transaction).

Трансформируем написанный скрипт в кодировку base64.<br>
Установим скрипт в кодировке base64 на [аккаунт](#работа-с-аккаунтом) alice.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.SetScriptTransaction;

// Реализация через builder
Base64String script = node.compileScript("{-# SCRIPT_TYPE ACCOUNT #-} true").script();
SetScriptTransaction tx = SetScriptTransaction.builder(script).getSignedWith(alice);
   
// Реализация через сonstructor
Base64String script = node.compileScript("{-# SCRIPT_TYPE ACCOUNT #-} true").script();
SetScriptTransaction tx = new SetScriptTransaction(alice.publicKey(), script).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("script:" + txInfo.tx().script().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| script <span style="color:red">(required)</span> | Скомпилированный скрипт в кодировке base64.<br>Размер скрипта аккаунта — до 8192 байт.<br>Размер скрипта dApp — до 32 768 байт (после активации фичи № 17 — до 163 840 байт).<br>null — удалить скрипт| [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/set-script-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=base64,AAIDAAAAAAAAAAYIARIAEgAAAAACAQAAAApyYW5kb21pemVyAAAAAQAAAANpbnYEAAAACGxhc3RQbGF5BAAAAAckbWF0Y2gwCQAEHAAAAAIFAAAABHRoaXMCAAAACGxhc3RQbGF5AwkAAAEAAAACBQAAAAckbWF0Y2gwAgAAAApCeXRlVmVjdG9yBAAAAAFzBQAAAAckbWF0Y2gwBQAAAAFzAwkAAAEAAAACBQAAAAckbWF0Y2gwAgAAAARVbml0BAAAAAFhBQAAAAckbWF0Y2gwAQAAAAxXYXZlc0xvdHRvVjIJAQAAAAV0aHJvdwAAAAAEAAAABHJhbmQJAADLAAAAAgkAAMsAAAACCQAAywAAAAIJAADLAAAAAgkAAMsAAAACBQAAAAhsYXN0UGxheQgFAAAAA2ludgAAAA10cmFuc2FjdGlvbklkCAUAAAADaW52AAAAD2NhbGxlclB1YmxpY0tleQgFAAAACWxhc3RCbG9jawAAABNnZW5lcmF0aW9uU2lnbmF0dXJlCQABmgAAAAEIBQAAAAlsYXN0QmxvY2sAAAAJdGltZXN0YW1wCQABmgAAAAEIBQAAAAlsYXN0QmxvY2sAAAAGaGVpZ2h0CQAB9wAAAAEFAAAABHJhbmQBAAAACnN0YXJ0TG90dG8AAAABAAAAA2ludgQAAAAJcGxheUxpbWl0CQAAaQAAAAIJAQAAAAx3YXZlc0JhbGFuY2UAAAABBQAAAAR0aGlzAAAAAAAAAABkBAAAAAdwYXltZW50CQEAAAAHZXh0cmFjdAAAAAEIBQAAAANpbnYAAAAHcGF5bWVudAMJAQAAAAEhAAAAAQkBAAAACWlzRGVmaW5lZAAAAAEIBQAAAANpbnYAAAAHcGF5bWVudAkAAAIAAAABAgAAAB9TaG91bGQgYmUgd2l0aCBQYXltZW50IGluIFdhdmVzAwkBAAAACWlzRGVmaW5lZAAAAAEIBQAAAAdwYXltZW50AAAAB2Fzc2V0SWQJAAACAAAAAQIAAAAaUGF5bWVudCBzaG91bGQgYmUgaW4gV2F2ZXMDCQAAZgAAAAIIBQAAAAdwYXltZW50AAAABmFtb3VudAUAAAAJcGxheUxpbWl0CQAAAgAAAAEJAAEsAAAAAgIAAAAcUGF5bWVudCBzaG91bGQgYmUgbGVzcyB0aGFuIAkAAaQAAAABBQAAAAlwbGF5TGltaXQEAAAACHJhbmRoYXNoCQEAAAAKcmFuZG9taXplcgAAAAEFAAAAA2ludgQAAAALd2luVHJhbnNmZXIJAQAAAAtUcmFuc2ZlclNldAAAAAEJAARMAAAAAgkBAAAADlNjcmlwdFRyYW5zZmVyAAAAAwgFAAAAA2ludgAAAAZjYWxsZXIJAABpAAAAAgkAAGgAAAACCAUAAAAHcGF5bWVudAAAAAZhbW91bnQAAAAAAAAAAL4AAAAAAAAAAGQFAAAABHVuaXQFAAAAA25pbAQAAAANd3JpdGVMYXN0UGxheQkBAAAACFdyaXRlU2V0AAAAAQkABEwAAAACCQEAAAAJRGF0YUVudHJ5AAAAAgIAAAAIbGFzdFBsYXkFAAAACHJhbmRoYXNoBQAAAANuaWwDCQAAZgAAAAIAAAAAAAAAAfQJAABqAAAAAgkABLEAAAABBQAAAAhyYW5kaGFzaAAAAAAAAAAD6AkBAAAADFNjcmlwdFJlc3VsdAAAAAIFAAAADXdyaXRlTGFzdFBsYXkFAAAAC3dpblRyYW5zZmVyCQEAAAAMU2NyaXB0UmVzdWx0AAAAAgUAAAANd3JpdGVMYXN0UGxheQkBAAAAC1RyYW5zZmVyU2V0AAAAAQUAAAADbmlsAAAAAgAAAANpbnYBAAAABWxvdHRvAAAAAAkBAAAACnN0YXJ0TG90dG8AAAABBQAAAANpbnYAAAADaW52AQAAAAdkZWZhdWx0AAAAAAkBAAAACnN0YXJ0TG90dG8AAAABBQAAAANpbnYAAAAA4XqnJg%3D%3D)|
<!-- | account <span style="color:red">(required)| Адрес получателя в кодировке base58 или [псевдоним](https://docs.waves.tech/ru/blockchain/account/alias) адреса c префиксом alias:<байт_сети>:.<br>Например alias:T:merry (см.[Байт сети)](https://docs.waves.tech/ru/blockchain/blockchain-network/#%D0%B1%D0%B0%D0%B9%D1%82-%D1%81%D0%B5%D1%82%D0%B8) | 3PFmoN5YLoPNsL4cmNGkRxbUKrUVntwyAhf<br>(alice)| -->

<br>

<!-- Invoke script transaction = Транзакция вызова скрипта -->
#### `Invoke script transaction` ####
Транзакция вызова скрипта выполняет вызов функции [dApp](https://docs.waves.tech/ru/blockchain/account/dapp).<br>
Подробнее о [транзакции вызова скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/invoke-script-transaction).<br>
Подробнее о [dApp и о вызове скрипта](https://docs.waves.tech/ru/building-apps/smart-contracts/what-is-a-dapp).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Exchange transaction = Транзакция обмена -->
#### `Exchange transaction` ####
Транзакция обмена обменивает два различных токена между двумя аккаунтами.<br>
Подробнее о [транзакции обмена](https://docs.waves.tech/ru/blockchain/transaction-type/exchange-transaction).

Транзакция содержит два встречных ордера: ордер на покупку и ордер на продажу. 
Блокчейн гарантирует, что условия обмена не хуже, чем указаны в каждом из ордеров.


```Java
// Необходимые импортирования
import java.util.Arrays;
import com.wavesplatform.transactions.ExchangeTransaction;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.common.Amount;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.exchange.Order;
import com.wavesplatform.transactions.exchange.OrderType;
import com.wavesplatform.wavesj.info.ExchangeTransactionInfo;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();

Amount amount = Amount.of(1);
Amount price = Amount.of(100, assetId);
long matcherFee = 300000;

Order buy = Order.builder(OrderType.BUY, amount, price, alice.publicKey()).getSignedWith(bob);
Order sell = Order.builder(OrderType.SELL, amount, price, alice.publicKey()).getSignedWith(alice);
ExchangeTransaction tx = ExchangeTransaction
        .builder(buy, sell, amount.value(), price.value(), matcherFee, matcherFee)
        .getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();

Amount amount = Amount.of(1);
Amount price = Amount.of(100, assetId);
long matcherFee = 300000;

Order buy = new Order(bob.publicKey(), OrderType.BUY, amount, price, alice.publicKey()).addProof(bob);
Order sell = new Order(alice.publicKey(), OrderType.SELL, amount, price, alice.publicKey()).addProof(alice);

ExchangeTransaction tx = new ExchangeTransaction(
        alice.publicKey(),buy,sell,amount.value(),price.value(),matcherFee,matcherFee)
        .addProof(alice);


// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());
ExchangeTransactionInfo txInfo = node.getTransactionInfo(tx.id(), ExchangeTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());

System.out.println("order1 version:" + txInfo.tx().buyOrder().version());
System.out.println("order1 id:" + txInfo.tx().buyOrder().id());
System.out.println("order1 sender:" + txInfo.tx().buyOrder().sender().address().encoded());
System.out.println("order1 senderPublicKey:" + txInfo.tx().buyOrder().sender().encoded());
System.out.println("order1 matcherPublicKey:" + txInfo.tx().buyOrder().matcher().encoded());
System.out.println("order1 assetPair amountAsset:" + txInfo.tx().buyOrder().assetPair().left().encoded());
System.out.println("order1 assetPair priceAsset:" + txInfo.tx().buyOrder().assetPair().right().encoded());
System.out.println("order1 orderType:" + txInfo.tx().buyOrder().type());
System.out.println("order1 amount:" + txInfo.tx().buyOrder().amount().value());
System.out.println("order1 price:" + txInfo.tx().buyOrder().price().value());
System.out.println("order1 timestamp:" + txInfo.tx().buyOrder().timestamp());
System.out.println("order1 expiration:" + txInfo.tx().buyOrder().expiration());
System.out.println("order1 matcherFee:" + txInfo.tx().buyOrder().fee().value());
System.out.println("order1 signature:" + txInfo.tx().buyOrder().proofs().get(0));
System.out.println("order1 proofs:" + txInfo.tx().buyOrder().proofs().get(0));
System.out.println("order1 matcherFeeAssetId:" + txInfo.tx().buyOrder().fee().assetId().encoded());
System.out.println("order1 eip712Signature:" + Arrays.toString(txInfo.tx().buyOrder().eip712Signature()));

System.out.println("order2 version:" + txInfo.tx().sellOrder().version());
System.out.println("order2 id:" + txInfo.tx().sellOrder().id());
System.out.println("order2 sender:" + txInfo.tx().sellOrder().sender().address().encoded());
System.out.println("order2 senderPublicKey:" + txInfo.tx().sellOrder().sender().encoded());
System.out.println("order2 matcherPublicKey:" + txInfo.tx().sellOrder().matcher().encoded());
System.out.println("order2 assetPair amountAsset:" + txInfo.tx().sellOrder().assetPair().left().encoded());
System.out.println("order2 assetPair priceAsset:" + txInfo.tx().sellOrder().assetPair().right().encoded());
System.out.println("order2 orderType:" + txInfo.tx().sellOrder().type());
System.out.println("order2 amount:" + txInfo.tx().sellOrder().amount().value());
System.out.println("order2 price:" + txInfo.tx().sellOrder().price().value());
System.out.println("order2 timestamp:" + txInfo.tx().sellOrder().timestamp());
System.out.println("order2 expiration:" + txInfo.tx().sellOrder().expiration());
System.out.println("order2 matcherFee:" + txInfo.tx().sellOrder().fee().value());
System.out.println("order2 signature:" + txInfo.tx().sellOrder().proofs().get(0));
System.out.println("order2 proofs:" + txInfo.tx().sellOrder().proofs().get(0));
System.out.println("order2 matcherFeeAssetId:" + txInfo.tx().sellOrder().fee().assetId().encoded());
System.out.println("order2 eip712Signature:" + Arrays.toString(txInfo.tx().sellOrder().eip712Signature()));

System.out.println("amount:" + txInfo.tx().amount());
System.out.println("price:" + txInfo.tx().price());
System.out.println("buyMatcherFee:" + txInfo.tx().buyMatcherFee());
System.out.println("sellMatcherFee:" + txInfo.tx().sellMatcherFee());

System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  amount | Количество amount-ассета.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») amount-ассета | 1000 |
| price | Стоимость 1 amount-ассета, выраженная в price-ассете, умноженная на коэффициент:<br>• 10<sup>8 для транзакции обмена версии 3</sup>;<br>• 10<sup>8 + priceAssetDecimals – amountAssetDecimals для транзакции обмена версии 2 или 1,<br>где amountAssetDecimals, priceAssetDecimals — количество знаков после запятой (параметр токена)</sup>| 1134500 |
| buyMatcherFee | Комиссия матчера за выполнение ордера на покупку. ID токена комиссии указан в ордере на покупку.| 300000 |
| sellMatcherFee | Комиссия матчера за выполнение ордера на продажу. ID токена комиссии указан в ордере на продажу. | 750 | 
| order1, order2 | Ордер на покупку и ордер на продажу. См. раздел [Oрдер](https://docs.waves.tech/ru/blockchain/order). | [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/exchange-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=%22order1%22%3A,2gvqaYy2BFbK4BJZS8taRJnhgfQ1z2CytF2RqjcyEfzFiu9tkTjN5q4UyFXpPqS3E6eD2WQBUaYCTYDKv98iW1sy%22%0A%20%20%20%20%5D%0A%20%20%7D)|


<br>

<!-- Mass transfer transaction = Транзакция массового перевода  -->
#### `Mass transfer transaction` ####
Транзакция массового перевода выполняет перевод токена нескольким получателям (от 1 до 100).<br>
Подробнее о [транзакции массового перевода](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction).


<!-- Выполним транзакцию массового перевода на аккаунт bob с аккаунта alice. -->

```Java
// Необходимые импортирования
import java.util.Collections;
import com.wavesplatform.transactions.mass.Transfer;
import com.wavesplatform.transactions.MassTransferTransaction;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.wavesj.info.MassTransferTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;

// Реализация через builder
MassTransferTransaction tx = MassTransferTransaction.builder(Transfer.to(bob.address(), 1000)).getSignedWith(alice);
        node.waitForTransaction(node.broadcast(tx).id());

// Реализация через сonstructor
MassTransferTransaction tx = new MassTransferTransaction(alice.publicKey(), AssetId.WAVES, Collections
        .singletonList(Transfer.to(bob.address(), 1000)),null).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
MassTransferTransactionInfo txInfo = node.getTransactionInfo(tx.id(), MassTransferTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("attachment:" + txInfo.tx().attachment().encoded());
System.out.println("transferCount:" + txInfo.tx().transfers().size());
System.out.println("totalAmount:" + txInfo.tx().total());
System.out.println("transfers recipient:" + txInfo.tx().transfers().get(0).recipient().toString());
System.out.println("transfers amount:" + txInfo.tx().transfers().get(0).amount());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId <span style="color:red">(required)</span> | ID переводимого токена в кодировке base58.<br>null — перевод в WAVES. | Fx2rhWK36H1nfXsiD4orNpBm2QG1JrMhx3eUcPVcoZm2 |
| transfers.recipient | Адрес получателя в кодировке base58 или псевдоним адреса c префиксом alias:<байт_сети>:,<br>например alias:T:merry (см. Байт сети). | [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=%22transfers%22%3A,...%0A%20%20%5D%2C)|
| transfers.amount | Количество токена к переводу.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена. | [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=%22transfers%22%3A,%2C%0A%20%20%20%20...%0A%20%20%5D) |
| transferCount | Количество получателей. | 100 |
| totalAmount | Сумма переводов в транзакции.| 100000 |
| attachment | Произвольные данные (обычно комментарий к транзакции), до 140 байт, в кодировке base58. | [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=xZBWqm9Ddt5BJVFvHUaQwB7Dsj78UQ5HatQjD8VQKj4CHG48WswJxUUeHEDZJkHgt9LycUpHBFc8ENu8TF8vvnDJCgfy1NeKaUNydqy9vkACLZjSqaVmvfaM3NQB)|

<br>

<!-- Data transaction = Транзакция данных -->
#### `Data transaction` ####
Транзакция данных добавляет, удаляет или изменяет записи в [хранилище данных аккаунта](https://docs.waves.tech/ru/blockchain/account/account-data-storage) отправителя.<br>
Подробнее о [транзакции данных](https://docs.waves.tech/ru/blockchain/transaction-type/data-transaction).

<br>

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.DataTransaction;
import com.wavesplatform.transactions.data.StringEntry;
import com.wavesplatform.wavesj.info.DataTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;
import java.util.Collections;

// Реализация через builder
DataTransaction tx = DataTransaction.builder(StringEntry.as("str", alice.address().toString())).getSignedWith(alice);

// Реализация через сonstructor
DataTransaction tx = new DataTransaction(
        alice.publicKey(),Collections.singletonList(StringEntry.as("str", alice.address().encoded())))
        .addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());

TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
DataTransactionInfo txInfo = node.getTransactionInfo(tx.id(), DataTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("data key:" + txInfo.tx().data().get(0).key());
System.out.println("data type:" + txInfo.tx().data().get(0).type().name());
System.out.println("data value:" + txInfo.tx().data().get(0).valueAsObject());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());


```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| data.key <span style="color:red">(required)</span> | Ключ записи. Строка, до 400 байт для версии 2, до 100 символов для версии 1. | bool |
| data.type | Тип данных:<br>- binary<br>- boolean<br>- integer<br>- string<br>null — удаление записи | boolean |
| data.value | Значение записи. До 32 767 байт. Бинарное значение должно быть в кодировке base64.<br>null — удаление записи| 1234567 |

<br>

<!-- Create alias transaction = Транзакция создания псевдонима -->
#### `Create alias transaction` ####
Транзакция создания псевдонима создает [псевдоним](https://docs.waves.tech/ru/blockchain/account/alias) для адреса отправителя.<br>
Подробнее о [транзакции создания псевдонима](https://docs.waves.tech/ru/blockchain/transaction-type/create-alias-transaction).

<br>


```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Alias;
import com.wavesplatform.transactions.CreateAliasTransaction;
import com.wavesplatform.wavesj.info.CreateAliasTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;

// Реализация через builder
Alias alias = Alias.as("alice_" + System.currentTimeMillis());
CreateAliasTransaction tx = CreateAliasTransaction.builder(alias.toString()).getSignedWith(alice);

// Реализация через сonstructor
Alias alias = Alias.as("alice_" + System.currentTimeMillis());
CreateAliasTransaction tx = new CreateAliasTransaction(alice.publicKey(), alias.toString()).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней
node.waitForTransaction(node.broadcast(tx).id());

TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
CreateAliasTransactionInfo txInfo = node.getTransactionInfo(tx.id(), CreateAliasTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("alias:" + txInfo.tx().alias().toString());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  alias <span style="color:red">(required)</span> | Псевдоним. См. [Требования к псевдониму](https://docs.waves.tech/ru/blockchain/account/alias#%D1%82%D1%80%D0%B5%D0%B1%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F-%D0%BA-%D0%BF%D1%81%D0%B5%D0%B2%D0%B4%D0%BE%D0%BD%D0%B8%D0%BC%D1%83) | 91f452553298770f |


<br>

---

**Сеть** <br>

<!-- Lease transaction = Транзакция лизинга -->
#### `Lease transaction` ####
Транзакция лизинга передает WAVES в лизинг другому аккаунту.<br>
Подробнее о [транзакции лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-transaction).

Передаем токен в лизинг [пользователю](#работа-с-аккаунтом) bob с аккаунта отправителя alice в размере 1000.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.LeaseTransaction;
import com.wavesplatform.wavesj.info.LeaseTransactionInfo;

// Реализация через builder
LeaseTransaction tx = LeaseTransaction.builder(bob.address(), 1000).getSignedWith(alice);

// Реализация через сonstructor
LeaseTransaction tx = LeaseTransaction tx = new LeaseTransaction(
        alice.publicKey(),bob.address(),1000).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());

LeaseTransactionInfo txInfo = node.getTransactionInfo(tx.id(), LeaseTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("amount:" + txInfo.tx().amount());
System.out.println("recipient:" + txInfo.tx().recipient().toString());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| amount <span style="color:red">(required)</span> | Количество WAVELET, передаваемое в лизинг (то есть количество WAVES, умноженное на 10<sup>8</sup>). | 1000 |
| recipient | Адрес получателя в кодировке base58 или псевдоним адреса. | 3P2HNUd5VUPLMQkJmctTPEeeHumiPN2GkTb | 
| status | Статус лизинга:<br>- active — лизинг действует.<br>- canceled — лизинг отменен,<br>см. [Транзакция отмены лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-cancel-transaction).| active | 



<!-- Lease cancel transaction = Транзакция закрытия лизинга -->
#### `Lease cancel transaction` ####
Транзакция закрытия лизинга прекращает [лизинг](#lease-transaction).<br>
Подробнее о [транзакции закрытия лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-cancel-transaction).


Создадим тестовую [транзакцию лизинга](#lease-transaction).
Отменим транзакцию лизинга и подпишем её приватным ключем пользователя alice.

```Java
// Необходимые импортирования
import com.wavesplatform.wavesj.info.LeaseCancelTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;
import com.wavesplatform.transactions.LeaseCancelTransaction;
import com.wavesplatform.transactions.LeaseTransaction;

// Реализация через builder
long amount = 1000;
LeaseTransaction leaseTx = LeaseTransaction.builder(bob.address(), amount).getSignedWith(alice);
int leaseHeight = node.waitForTransaction(node.broadcast(leaseTx).id()).height();
LeaseCancelTransaction tx = LeaseCancelTransaction.builder(leaseTx.id()).getSignedWith(alice);

// Реализация через сonstructor
long amount = 1000;
LeaseTransaction leaseTx = new LeaseTransaction(alice.publicKey(), bob.address(), amount).addProof(alice);
int leaseHeight = node.waitForTransaction(node.broadcast(leaseTx).id()).height();
LeaseCancelTransaction tx = new LeaseCancelTransaction(alice.publicKey(), leaseTx.id()).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());

TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
LeaseCancelTransactionInfo txInfo = node.getTransactionInfo(tx.id(), LeaseCancelTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("leaseId:" + txInfo.tx().leaseId().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
System.out.println("lease id:" + txInfo.leaseInfo().id().encoded());
System.out.println("lease originTransactionId:" + txInfo.leaseInfo().originTransactionId().encoded());
System.out.println("lease sender:" + txInfo.leaseInfo().sender().encoded());
System.out.println("lease recipient:" + txInfo.leaseInfo().recipient().toString());
System.out.println("lease recipient:" + txInfo.leaseInfo().recipient().toString());
System.out.println("lease amount:" + txInfo.leaseInfo().amount());
System.out.println("lease height:" + txInfo.leaseInfo().height());
System.out.println("lease status:" + txInfo.leaseInfo().status().toString());
System.out.println("lease cancelHeight:" + txInfo.leaseInfo().cancelHeight().getAsInt());
System.out.println("lease cancelTransactionId:" + txInfo.leaseInfo().cancelTransactionId().get().encoded());
```

<br>

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| leaseId <span style="color:red">(required)</span> | Идентификатор транзакции лизинга, который нужно отменить. | BhHPPHBZpfp8FBy8DE7heTpWGJySYg2uU2r4YM6qaisw |
| lease | Параметры отмененного лизинга. | [Пример](https://docs.waves.tech/ru/blockchain/transaction-type/lease-cancel-transaction#json-%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5:~:text=%22lease%22%3A,%22canceled%22%0A%20%20%7D)|

<br>

<!-- Sponsor fee transaction = Транзакция спонсирования -->
#### `Sponsor fee transaction` ####
Транзакция спонсирования устанавливает или отменяет спонсирование.<br>
Подробнее о [транзакции спонсирования](https://docs.waves.tech/ru/blockchain/transaction-type/sponsor-fee-transaction).




```Java
// Необходимые импортирования
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.SponsorFeeTransaction;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.wavesj.info.SponsorFeeTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();
SponsorFeeTransaction tx = SponsorFeeTransaction.builder(assetId, 5).getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
SponsorFeeTransaction tx = new SponsorFeeTransaction(alice.publicKey(), assetId, 5).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());

TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
SponsorFeeTransactionInfo txInfo = node.getTransactionInfo(tx.id(), SponsorFeeTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("minSponsoredAssetFee:" + txInfo.tx().minSponsoredFee());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  minSponsoredAssetFee <span style="color:red">(required)</span> | Количество спонсорского ассета, эквивалентное 0,001 WAVES (100 000 WAVELET).<br>Целое число, выраженное в минимальных единицах («копейках») ассета.<br>null — отмена спонсирования.| 1000 |
| assetId | ID ассета в кодировке base58. | p1vuxnGyfH9VFiuyKmsh25rn6MedjGbQu7d6Zt1sY4U |

<br>

### Работа с нодой ###
