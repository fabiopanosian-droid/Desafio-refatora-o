


---

✅ CÓDIGO CORRIGIDO (apenas nomes nos comentários)

# ==============================
# ESTRATÉGIAS DE DESCONTO
# ==============================

class DiscountStrategy:
    def aplicar(self, valor_base):
        return valor_base

class PixDiscount(DiscountStrategy):
    def aplicar(self, valor_base):
        print("Aplicando 5% de desconto PIX.")
        return valor_base * 0.95

class LargeOrderDiscount(DiscountStrategy):
    def aplicar(self, valor_base):
        print("Aplicando 10% de desconto para pedidos grandes.")
        return valor_base * 0.90


# ==============================
# ESTRATÉGIAS DE FRETE
# ==============================

class ShippingStrategy:
    def calcular(self, valor):
        return 0.0

class NormalShipping(ShippingStrategy):
    def calcular(self, valor):
        custo = valor * 0.05
        print(f"Frete Normal: R${custo:.2f}")
        return custo

class ExpressoShipping(ShippingStrategy):
    def calcular(self, valor):
        custo = valor * 0.10 + 15.00
        print(f"Frete Expresso (com taxa): R${custo:.2f}")
        return custo

class TeleportShipping(ShippingStrategy):
    def calcular(self, valor):
        print(f"Frete Teletransporte: R$50.00")
        return 50.00


# ==============================
# ESTRATÉGIAS DE PAGAMENTO
# ==============================

class PaymentStrategy:
    def pagar(self, valor):
        raise NotImplementedError

class CreditPayment(PaymentStrategy):
    def pagar(self, valor):
        print(f"Processando R${valor:.2f} via Crédito...")
        if valor < 1000:
            print("   -> Pagamento APROVADO.")
            return True
        print("   -> Pagamento REJEITADO (limite excedido).")
        return False

class PixPayment(PaymentStrategy):
    def pagar(self, valor):
        print(f"Processando R${valor:.2f} via PIX...")
        print("   -> Pagamento APROVADO (QR Code gerado).")
        return True

class ManaPayment(PaymentStrategy):
    def pagar(self, valor):
        print(f"Processando R${valor:.2f} via Mana...")
        print("   -> Pagamento APROVADO (aguardando estabilidade energética).")
        return True


# ==============================
# FÁBRICAS (para remover ifs espalhados)
# ==============================

def escolher_desconto(metodo_pagamento, valor_base):
    if metodo_pagamento == "Pix":
        return PixDiscount()
    if valor_base > 500:
        return LargeOrderDiscount()
    return DiscountStrategy()

def escolher_frete(tipo):
    return {
        "Normal": NormalShipping(),
        "Expresso": ExpressoShipping(),
        "Teletransporte": TeleportShipping()
    }.get(tipo, ShippingStrategy())

def escolher_pagamento(metodo):
    return {
        "Credito": CreditPayment(),
        "Pix": PixPayment(),
        "Mana": ManaPayment()
    }.get(metodo, PaymentStrategy())


# ==============================
# FACHADA (Facade) → Checkout
# ==============================

class Checkout:
    def __init__(self, itens, metodo_pagamento, tipo_frete, tem_embalagem_presente=False):
        self.valor_base = sum(item["valor"] for item in itens)
        self.desconto = escolher_desconto(metodo_pagamento, self.valor_base)
        self.frete = escolher_frete(tipo_frete)
        self.pagamento = escolher_pagamento(metodo_pagamento)
        self.tem_embalagem = tem_embalagem_presente

    def finalizar(self):
        print("\n=== CHECKOUT REFORMADO ===")

        valor = self.desconto.aplicar(self.valor_base)
        valor += self.frete.calcular(valor)

        if self.tem_embalagem:
            print("Adicionando R$5.00 pela Embalagem de Presente.")
            valor += 5.00

        print(f"Total: R${valor:.2f}")

        if self.pagamento.pagar(valor):
            print("✅ Pedido Finalizado com Sucesso!")
        else:
            print("❌ Erro no Pagamento. Compra Cancelada.")


# ==============================
# TESTES (iguais aos originais)
# ==============================

if __name__ == "__main__":
    itens1 = [
        {"nome": "Capa da Invisibilidade", "valor": 150.0},
        {"nome": "Poção de Voo", "valor": 80.0}
    ]
    Checkout(itens1, "Pix", "Normal").finalizar()

    print("\n--- Próximo Pedido ---")

    itens2 = [
        {"nome": "Cristal Mágico", "valor": 600.0}
    ]
    Checkout(itens2, "Credito", "Expresso", 
