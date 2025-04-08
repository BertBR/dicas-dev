### ✅ **Objetivo da Feature**
Antes de tudo, defina qual é o foco principal do cashback:
- Aumentar frequência de compra?
- Atrair novos usuários?
- Aumentar ticket médio?
- Estimular compras em mercados específicos?
- Fidelizar clientes?

Cada objetivo pode impactar o modelo de cashback escolhido.

---

### 🧭 **Planejamento Estratégico – Passo a Passo**

---

#### 1. **Análise Financeira e de Margem**
- **Calcule sua margem líquida média por pedido.**
- Defina um **limite de % de cashback viável** por pedido (ex: até 5%, 7%, 10%).
- Verifique se você terá **subvenção dos supermercados/parceiros** para custear o cashback.
    - **Modelo mais sustentável**: dividir o custo entre marketplace e loja.

---

#### 2. **Definir as Regras do Cashback**
- **Critérios para ganhar cashback**:
  - Apenas em compras acima de R$X?
  - Apenas em determinadas categorias?
  - Apenas em supermercados parceiros?
  - Apenas se pagar via meio de pagamento específico (ex: carteira digital)?
- **Valor do cashback**:
  - Fixo (ex: R$5 por compra)?
  - Percentual do valor da compra (ex: 5%)?
  - Variável por produto (ideal se o mercado repassa parte do custo)?
- **Forma de resgate**:
  - Em compras futuras?
  - Transferência para carteira digital?
  - Expiração do saldo após X dias?

---

#### 3. **Arquitetura Técnica / Backend**
- Criar estrutura de controle do "saldo de cashback" por usuário.
- Vincular o cashback a pedidos elegíveis.
- Registrar:
  - **Valor gerado**
  - **Status** (pendente, liberado, expirado, usado)
  - **Data de liberação** (ex: após confirmação do pedido)
- Criar sistema de notificação e histórico para o usuário ver:
  - Cashback acumulado
  - Uso do cashback
  - Validade
- Possibilitar integração com a carteira ou meio de pagamento do app.

---

#### 4. **Fluxo Operacional e de Negócio**
- Cashback é liberado **após confirmação da entrega**, ou outro evento de segurança?
- Como será a **reconciliação com os mercados parceiros** (se eles dividirem o custo)?
- Haverá campanhas específicas (ex: cashback em dobro na Black Friday)?

---

#### 5. **UX e Comunicação com o Usuário**
- Mostre o valor do cashback na tela do produto/carrinho/checkout.
- Envie notificações:
  - Quando o cashback for liberado.
  - Quando estiver prestes a expirar.
- Mostre o saldo no perfil do usuário.

---

#### 6. **Segurança e Prevenção de Fraudes**
- Evite abuso do cashback criando **limites por CPF/dispositivo**.
- Valide tentativas de “compra e cancelamento para ganhar cashback”.
- Monitore comportamento suspeito com inteligência antifraude.

---

#### 7. **Métricas de Sucesso (KPIs)**
- Quantidade de usuários que usam cashback
- Retorno de clientes (retenção)
- Aumento de ticket médio
- Frequência de compra
- Custo total do programa vs. ROI

---

### 💡 **Dicas Extras**
- **Gamifique** o uso do cashback com selos, conquistas, níveis.
- Combine com programas de fidelidade (cashback + pontos).
- Ofereça "cashback boost" em parcerias especiais.
- Estude plataformas como **Méliuz, Ame Digital, PicPay** e **iFood**, que têm bons cases.

---

## 🔧 Parte 1: **Modelo de Banco de Dados para Cashback**

Aqui está uma estrutura base que você pode adaptar à sua stack:

### 🔹 **Tabela: `users_cashback_wallet`**
Armazena o saldo de cashback de cada usuário.

| id | user_id | total_balance | available_balance | pending_balance | last_updated_at |
|----|---------|----------------|--------------------|------------------|-----------------|
| 1  | 123     | 45.00           | 30.00               | 15.00             | 2025-04-08      |

---

### 🔹 **Tabela: `cashback_transactions`**
Registra cada movimento (entrada ou uso de cashback).

| id | user_id | order_id | type        | value | status     | created_at | expires_at |
|----|---------|----------|-------------|-------|------------|------------|------------|
| 1  | 123     | 789      | earned      | 15.00 | pending    | 2025-04-08 | 2025-05-08 |
| 2  | 123     | NULL     | redeemed    | 10.00 | completed  | 2025-04-10 | NULL       |

**Status possíveis**:
- `pending` (aguardando entrega ou confirmação)
- `available` (liberado)
- `expired` (prazo passou)
- `redeemed` (usado)
- `cancelled` (pedido cancelado)

---

## 🛠 Parte 2: **Fluxo de API e Back-End**

Aqui está um fluxo básico dos endpoints:

### ✅ Quando o usuário faz uma compra:

1. `POST /order`
2. Após confirmação de pagamento:
   - Registra cashback potencial (`status = pending`)
   - Calcula valor (porcentagem ou fixo)
   - Associa ao pedido

---

### 🧾 Após a entrega confirmada:

1. Um job automático ou webhook do mercado:
   - Altera status do cashback para `available`
   - Atualiza saldo em `users_cashback_wallet`

---

### 💸 Para resgatar cashback:

1. `POST /checkout` com uso de cashback
   - Valida se o saldo está disponível
   - Debita valor da carteira
   - Registra transação com `type = redeemed`

---

## 📊 Parte 3: **Simulação Financeira**

Vamos montar uma base simples pra simular se o cashback vai gerar prejuízo. Me responde rapidinho:

1. Qual o **ticket médio** de compra no seu app?
2. Qual a **margem de lucro líquida** estimada do app sobre cada compra?
3. Qual percentual de cashback você quer oferecer? (ex: 5%)

Com isso eu monto uma simulação que mostra:
- Custo do cashback por pedido
- Lucro líquido estimado por pedido com e sem cashback
- Ponto de equilíbrio

---

## 🎁 Parte 4: Estratégias para não ter prejuízo

Enquanto você responde os dados acima, aqui vão algumas ideias pra manter o cashback sustentável:

| Estratégia                         | Como ajuda                                                       |
|-----------------------------------|------------------------------------------------------------------|
| Cashback em produtos com margem alta | Foca onde você tem mais folga financeira                         |
| Cashback limitado por mês         | Ex: até R$20 por usuário/mês                                     |
| Cashback com validade curta       | Estimula recompra rápida + evita saldo acumulado                 |
| Cashback só com carteira digital  | Reduz taxas de transação e mantém o dinheiro no seu ecossistema  |
| Cashback com patrocínio dos mercados | Divide ou repassa o custo para os parceiros                      |

---

## 🎯 Parte 1: **Padrões de Cashback no Mercado**

Empresas usam esses padrões com frequência:

| Cashback (%) | Onde é comum?                                    | Observações                                                                 |
|--------------|---------------------------------------------------|------------------------------------------------------------------------------|
| **1% a 3%**  | Supermercados, delivery, bancos digitais          | Baixo impacto financeiro, bom para fidelização                              |
| **5% a 10%** | Promoções específicas, novos usuários, categorias | Deve ser limitado no tempo, por parceiro ou valor de compra                 |
| **>10%**     | Campanhas pontuais, lançamento, boost temporário  | Alto risco — use só com apoio do parceiro ou com cashback condicional       |

📌 **Recomendado pra início**:
- Comece com **3%**.
- Limite: cashback máximo de **R$10 por pedido** ou **R$30 por mês por usuário**.
- Cashback **expira em 30 dias**, só pode ser usado dentro do app.

---

## 🧩 Parte 2: **Modelo de Dados em SQL (PostgreSQL)**

### 🎲 1. Tabela: `users_cashback_wallet`

```sql
CREATE TABLE users_cashback_wallet (
    id SERIAL PRIMARY KEY,
    user_id UUID NOT NULL UNIQUE,
    total_balance NUMERIC(10,2) DEFAULT 0.0,
    available_balance NUMERIC(10,2) DEFAULT 0.0,
    pending_balance NUMERIC(10,2) DEFAULT 0.0,
    last_updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_users_cashback_user_id ON users_cashback_wallet(user_id);
```

---

### 🎲 2. Tabela: `cashback_transactions`

```sql
CREATE TABLE cashback_transactions (
    id SERIAL PRIMARY KEY,
    user_id UUID NOT NULL,
    order_id UUID,
    type VARCHAR(20) CHECK (type IN ('earned', 'redeemed')),
    value NUMERIC(10,2) NOT NULL,
    status VARCHAR(20) CHECK (status IN ('pending', 'available', 'redeemed', 'expired', 'cancelled')),
    created_at TIMESTAMP DEFAULT NOW(),
    expires_at TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users_cashback_wallet(user_id)
);

CREATE INDEX idx_cashback_user ON cashback_transactions(user_id);
CREATE INDEX idx_cashback_status ON cashback_transactions(status);
CREATE INDEX idx_cashback_order ON cashback_transactions(order_id);
```

---

## 🔄 Parte 3: **Principais Queries**

---

### ✅ Inserir novo cashback após compra

```sql
INSERT INTO cashback_transactions (user_id, order_id, type, value, status, expires_at)
VALUES ('UUID-DO-USUARIO', 'UUID-DO-PEDIDO', 'earned', 5.00, 'pending', NOW() + INTERVAL '30 days');
```

---

### ✅ Liberar cashback após entrega do pedido

```sql
UPDATE cashback_transactions
SET status = 'available'
WHERE order_id = 'UUID-DO-PEDIDO' AND status = 'pending';
```

> **Depois disso**: atualize a wallet!

```sql
UPDATE users_cashback_wallet
SET available_balance = available_balance + 5.00,
    pending_balance = pending_balance - 5.00,
    last_updated_at = NOW()
WHERE user_id = 'UUID-DO-USUARIO';
```

---

### ✅ Resgatar cashback no checkout

```sql
INSERT INTO cashback_transactions (user_id, type, value, status)
VALUES ('UUID-DO-USUARIO', 'redeemed', 10.00, 'redeemed');

UPDATE users_cashback_wallet
SET available_balance = available_balance - 10.00,
    total_balance = total_balance - 10.00,
    last_updated_at = NOW()
WHERE user_id = 'UUID-DO-USUARIO';
```

---

### ✅ Consultar extrato de cashback do usuário (últimos 30 dias)

```sql
SELECT 
    ct.id,
    ct.order_id,
    ct.type,
    ct.value,
    ct.status,
    ct.created_at,
    ct.expires_at
FROM cashback_transactions ct
JOIN users_cashback_wallet uw ON uw.user_id = ct.user_id
WHERE ct.user_id = 'UUID-DO-USUARIO'
  AND ct.created_at > NOW() - INTERVAL '30 days'
ORDER BY ct.created_at DESC;
```

---

### ✅ Verificar saldo total por usuário

```sql
SELECT 
    total_balance, 
    available_balance, 
    pending_balance
FROM users_cashback_wallet
WHERE user_id = 'UUID-DO-USUARIO';
```

---

## 🚀 Bônus: Automatização com Cron Jobs

- **Expiração automática de cashback (a cada dia)**:

```sql
UPDATE cashback_transactions
SET status = 'expired'
WHERE expires_at < NOW() AND status = 'available';

-- E atualiza a wallet
UPDATE users_cashback_wallet
SET available_balance = available_balance - (
    SELECT COALESCE(SUM(value), 0) 
    FROM cashback_transactions 
    WHERE status = 'expired' AND user_id = users_cashback_wallet.user_id
),
    total_balance = total_balance - (
    SELECT COALESCE(SUM(value), 0) 
    FROM cashback_transactions 
    WHERE status = 'expired' AND user_id = users_cashback_wallet.user_id
),
    last_updated_at = NOW()
WHERE EXISTS (
    SELECT 1 FROM cashback_transactions 
    WHERE status = 'expired' AND user_id = users_cashback_wallet.user_id
);
```