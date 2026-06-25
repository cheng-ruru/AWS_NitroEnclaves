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


##利用Nitro Enclaves來保護LLM推論<img width="1559" height="193" alt="image" src="https://github.com/user-attachments/assets/b578c063-6b1b-40c8-8d7c-c8d98e1b6cc6" />

<img width="1307" height="680" alt="image" src="https://github.com/user-attachments/assets/a440a669-e2be-464d-8a51-3e57ccb55bb6" />

