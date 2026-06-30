# AWS_NitroEnclaves
AWS Confidential Computing

# 什麼是機密運算（Confidential Computing）？

機密運算（Confidential Computing）是一種透過硬體安全機制保護**使用中資料（Data in Use）**的技術，補足傳統加密只能保護**靜態資料（Data at Rest）**與**傳輸中資料（Data in Transit）**的缺口。

## 為什麼需要機密運算？

傳統加密可以保護：

- 🔒 Data at Rest（靜態資料）
- 🔒 Data in Transit（傳輸中資料）

但當資料進入 CPU 或 Memory 進行運算時，通常會以**明文（Plaintext）**存在，因此可能面臨被竊取或窺探的風險。

## 機密運算如何運作？

機密運算透過 **Trusted Execution Environment（TEE）** 建立一個硬體隔離且可驗證的安全執行環境。

其特點包括：

- 在硬體層級提供資料保護
- 運算過程中的資料保持受到保護
- 支援遠端驗證（Remote Attestation）
- 降低雲端管理者、Hypervisor 或惡意程式存取資料的風險

## 適合保護哪些資料？

機密運算特別適合處理高敏感性資料，例如：

| 類型 | 說明 |
|------|------|
| PII | Personally Identifiable Information（個人識別資訊） |
| PHI | Protected Health Information（受保護健康資訊） |
| PFI | Personal Financial Information（個人金融資訊） |
| IP | Intellectual Property（智慧財產） |

## 常見應用場景

- 金融交易與風險分析
- 醫療資料分析
- AI 模型訓練與推論
- 區塊鏈錢包與私鑰保護
- 多方安全資料共享（Confidential Data Sharing）

# AWS Nitro System 
<img width="372" height="178" alt="image" src="https://github.com/user-attachments/assets/94cecae8-0c32-4915-be25-1419a4fd117a" />


AWS Nitro系統重新定義虛擬化架構，把管理與I/O處理功能從主機移到專用硬體設備，帶來更高效能、更安全的隔離環境，其核心如下：

包括一系列專用硬體卡片，像是：
- VPC
- EBS
- Instance Store
- 加密
- 安全監控等 I/O 功能

減輕主機負擔並提升效能。

## Nitro Security Chip
負責硬體層級包括：
- 安全啟動（Secure Boot）
- Instance 隔離

確保沒有人（包含 AWS 員工）能存取實體伺服器資料。

## NitroTPM
符合 TPM 2.0 規格，可在 EC2 Instance 提供硬體級別的 TPM 功能，用於：
- 金鑰處理（建立、儲存與使用）
- 完整性驗證等

## Nitro Hypervisor
輕量級 Hypervisor，只處理：
- CPU
- 記憶體

效能近似裸機（Bare Metal）。

# AWS Nitro Enclaves
<img width="361" height="178" alt="image" src="https://github.com/user-attachments/assets/1603c24e-1842-4f5e-a38b-5343c85cc351" />

AWS Nitro Enclaves 是在 EC2 Instance 中切出更小的信任邊界（隔離執行環境）。

Enclave 是獨立的、強化的（Hardened）且高度受限的虛擬機器（Virtual Machine）。

Parent Instance 僅能透過 **Secure Local Socket** 與 Enclave 溝通，其：
- Process
- Applications
- Kernel
- Users

均無法直接存取 Enclave 內部的資料或記憶體。

<img width="597" height="485" alt="image" src="https://github.com/user-attachments/assets/81864bb8-34f2-4915-a342-55038382d171" />


## 可整合服務

### AWS KMS（AWS Key Management Service）
- 加解密
- 金鑰生命週期管理

### AWS Certificate Manager（ACM）
- SSL/TLS 憑證管理

### AWS CloudTrail
- 追蹤所有使用者活動
- 記錄 API 呼叫行為

  ## Enclave 特性

- 沒有持久性儲存（Persistent Storage）
- 沒有互動式存取（Interactive Access）
- 沒有外部網路連接（External Network Connectivity）
- 無法透過 SSH 進入 Enclave

Enclave 內的資料和應用程式無法被 Parent Instance 的：
- Process
- Users（包含 root 或管理員）
- Kernel

直接存取，只有授權的程式碼（Authorized Code）能在 Enclave 中執行。

## 安全特性

因此：

- 秘密（Secrets）只會在 Enclave 內短暫存在
- 即使 Parent Instance 取得 root 權限，也無法讀取 Enclave 的記憶體

## 適用情境

可以將**最敏感的那一段**工作抽離到 Enclave 中執行，例如：

- 金鑰管理（Key Management）
- PII 解密（Personally Identifiable Information）
- 數位簽章（Digital Signature）
- AI／LLM 推論（Inference）

## Parent Instance 需求

- 基於 Nitro 的 Instance
- 具有至少 **4 個 vCPUs** 的 Intel 或 AMD 型 Instance
- 具有至少 **2 個 vCPUs** 的 AWS Graviton 型 Instance
- 並非支援所有 Instance，支援清單請參閱官方文件
- Linux 或 Windows（2016 或更新版本）作業系統

## Enclave 需求

- Linux 作業系統

## 如何使用 AWS Nitro Enclaves

- 每個 Parent Instance 最多可建立 **4 個 Enclave**
- Enclave 僅能與 Parent Instance 透過 **vsock** 通訊，Enclave 之間無法彼此通訊
- Parent Instance 必須處於執行狀態，Enclave 才會執行
- 使用 AWS Nitro Enclaves **無額外費用**

<img width="1734" height="824" alt="image" src="https://github.com/user-attachments/assets/cf037acb-f94d-4e25-8704-479ed4039435" />

<img width="1414" height="816" alt="image" src="https://github.com/user-attachments/assets/7c99bdab-6505-4cf9-92b2-6242bd4dc8e1" />

# 在 Linux 安裝 Nitro Enclaves CLI

## 1. 安裝 Nitro CLI

```bash
sudo dnf install aws-nitro-enclaves-cli -y
```

安裝 Nitro CLI，讓你可以執行：

- 建置 EIF（Enclave Image File）
- 啟動／停止 Enclave
- 查看 Enclave Console（Debug）

<img width="2000" height="201" alt="image" src="https://github.com/user-attachments/assets/99caa534-9b0a-44ed-8458-290ec0b8342e" />


## 2. 安裝建置 Enclave 映像所需的開發工具（包含範例）

```bash
sudo dnf install aws-nitro-enclaves-cli-devel -y
```

包含 EIF 建置所需的工具、範例與開發支援，可將 Docker Image 轉換為 EIF。

例如：

```bash
nitro-cli build-enclave --docker-uri ... --output-file app.eif
```

包含：

- EIF 建置工具
- 範例程式
- Helper 工具
- Build 相依套件

<img width="2000" height="377" alt="image" src="https://github.com/user-attachments/assets/bda28749-ef6b-482b-a850-885c0863da5b" />


## 3. 將使用者新增到 Nitro Enclaves 群組

```bash
sudo usermod -aG ne username
```

## 4. 將使用者新增到 Docker 群組

```bash
sudo usermod -aG docker username
```

## 5. 登出再重新登入

## 6. 驗證 Nitro CLI 是否正確安裝

```bash
nitro-cli --version
```

<img width="1594" height="262" alt="image" src="https://github.com/user-attachments/assets/e017bac3-4321-4966-b2fc-1e21aa11f790" />


## 7. 調整 CPU 與 RAM 配置

```bash
sudo vi /etc/nitro_enclaves/allocator.yaml
```

`allocator.yaml` 設定檔可決定：

- 要從 Parent Instance 切多少 Memory 給 Enclave
- 要從 Parent Instance 保留幾顆 vCPU 給 Enclave

<img width="1476" height="437" alt="image" src="https://github.com/user-attachments/assets/ce4bb53e-2531-41ed-8d84-7099aef894bb" />


## 8. 執行命令分配配置檔案中指定的資源

```bash
sudo systemctl enable --now nitro-enclaves-allocator.service
```

說明：

- `enable`：開機自動啟動
- `--now`：立即啟動服務


## 9. 啟動 Docker 服務

```bash
sudo systemctl enable --now docker
```

<img width="2000" height="163" alt="image" src="https://github.com/user-attachments/assets/4c88197f-97a8-4383-b50e-5c28c43c3a42" />

---

## 建構Enclave映像檔案

### 1. 從應用程式建構Docker範例映像：

```bash
docker build /usr/share/nitro_enclaves/examples/hello -t hello
```

> **說明：**
> Enclave不是直接吃程式碼，而是先將應用程式封裝成 Docker Image，再轉換成 Nitro Enclaves 可執行的 Enclave Image（EIF）。

<img width="2000" height="399" alt="image" src="https://github.com/user-attachments/assets/95355ef3-681c-4b63-bbfb-0612e0178442" />

### 2. 驗證已建構Docker映像：

```bash
docker image ls
```

Docker範例內容：hello

```bash
ls /usr/share/nitro_enclaves/examples/hello
```
<img width="1307" height="72" alt="image" src="https://github.com/user-attachments/assets/cd572eff-8c82-447b-a82a-30b4a11a6e61" />

```bash
cat /usr/share/nitro_enclaves/examples/hello/Dockerfile
```
<img width="1231" height="294" alt="image" src="https://github.com/user-attachments/assets/8b3418e9-282f-4d01-aff1-9c41a0ba3a01" />

```bash
cat /usr/share/nitro_enclaves/examples/hello/hello.sh
```
<img width="1225" height="324" alt="image" src="https://github.com/user-attachments/assets/430d37f3-d398-4b6c-afdf-91c5ee27e36c" />

> **說明：**
> 這個 Hello 範例包含 Dockerfile 與執行腳本（hello.sh），可以了解 Docker Image 是如何建立，以及 Enclave 啟動後會執行哪些內容。


### 3. 將Docker映像轉換為Enclave映像檔案：

```bash
nitro-cli build-enclave --docker-uri hello:latest --output-file hello.eif
```
<img width="1780" height="407" alt="image" src="https://github.com/user-attachments/assets/caf815cc-1eac-493c-9257-4c38d61c05e4" />

> **說明：**
> 此步驟會將 Docker Image 轉換成 AWS Nitro Enclaves 可執行的 **EIF（Enclave Image File）**。

產生Enclave的指紋，做硬體層級信任驗證（Attestation）。

> **補充：**
> 建立 EIF 時，Nitro CLI 會同時產生 **Image Measurements（PCR0、PCR1、PCR2）** 與映像雜湊值（SHA384）。這些量測值可用於 **Attestation**，讓外部服務（例如 AWS KMS）驗證目前執行的 Enclave 是否為預期且未遭竄改的映像，建立硬體層級的信任（Hardware Root of Trust）。

PCR(Platform Configuration Register)

PCR0：EIF(Enclave Image File)的量測值  
PCR1：Linux Kernel與啟動程式(Bootstrap)的量測值  
PCR2：應用程式的量測值  
PCR3：指派給Parent Instance的IAM Role的量測值  
PCR4：Parent Instance ID的量測值  
PCR8：Enclave映像檔簽署憑證的量測值  

> **補充：**
> PCR（Platform Configuration Register）是 Nitro Enclaves 用於 **Attestation（遠端驗證）** 的量測值（Measurements），用來證明目前執行的 Enclave 身分、映像內容及執行環境是否符合預期。外部服務（例如 AWS KMS）可依據這些 PCR 值決定是否信任該 Enclave。

---

## 執行Enclave

### 1.指定CPU、RAM和Enclave映像檔路徑：

```bash
nitro-cli run-enclave --cpu-count 1 --memory 512 --enclave-cid 16 --eif-path hello.eif --debug-mode
```
<img width="1968" height="408" alt="image" src="https://github.com/user-attachments/assets/81c00f9e-5f29-4f19-8162-9e3f4bfc037a" />

> **說明：**
> 此指令會建立一個新的 Enclave，並指定使用 1 個 vCPU、512 MB 記憶體，以及先前建立的 `hello.eif` 作為執行映像。


### 2.驗證Enclave正在執行：

```bash
nitro-cli describe-enclaves
```
<img width="1714" height="649" alt="image" src="https://github.com/user-attachments/assets/2c29b672-9f70-425a-bbd7-0b6b549ed09a" />

> **說明：**
> 可查看目前執行中的 Enclave 資訊，例如 Enclave ID、CID、CPU、記憶體配置及執行狀態。


### 3.由於使用 `--debug-mode`，可查看Enclave的唯讀控制台輸出：

```bash
nitro-cli console --enclave-id EnclaveID
```
這些輸出是來自一個沒有網路、沒有磁碟、不能SSH的Enclave。

<img width="2000" height="186" alt="image" src="https://github.com/user-attachments/assets/a2d4a48d-b5ca-4349-9713-36e797307022" />

<img width="792" height="236" alt="image" src="https://github.com/user-attachments/assets/30e39b3e-a80b-454c-b73a-399d7bb2fb03" />


> **補充：**
> `--debug-mode` 僅建議於開發與測試環境使用，正式環境通常不會啟用，以避免洩漏除錯資訊。


### 4.終止Enclave：

```bash
nitro-cli terminate-enclave --enclave-id EnclaveID
```

Enclave終止了代表：

- 無殘留機密
- 無磁碟殘留
- 無 swap
- 無 dump

<img width="1940" height="281" alt="image" src="https://github.com/user-attachments/assets/97207716-c6cc-4f9b-9414-a0767b7b9b0f" />

> **補充：**
> Enclave 終止後，其記憶體內容會立即銷毀，不會留下任何持久化資料，因此每次啟動都是一個全新的執行環境。這也是 Nitro Enclaves 能夠保護敏感資料的重要安全特性。
