# automacao-industrial-unifecaf
Trabalho Unifecaf 

Desafio de Automação Digital: Gestão de Peças, Qualidade e Armazenamento
Disciplina: Algoritmos e Lógica de Programação - UniFECAF

Sistema de controle de produção e qualidade industrial


# Configurações das regras de qualidade
PESO_MIN = 95
PESO_MAX = 105
CORES_VALIDAS = ["azul", "verde"]
COMPRIMENTO_MIN = 10
COMPRIMENTO_MAX = 20
CAPACIDADE_CAIXA = 10

# Estruturas de dados
pecas_aprovadas = []  # lista de dicts
pecas_reprovadas = []  # lista de dicts com motivo
caixas_fechadas = []  # lista de caixas (cada caixa é uma lista de peças)
caixa_atual = []      # caixa em uso

def avaliar_peca(peso, cor, comprimento):
    """
    Avalia se a peça atende aos critérios de qualidade.
    Retorna (aprovada: bool, motivos_reprovacao: list)
    """
    motivos = []

    if not (PESO_MIN <= peso <= PESO_MAX):
        motivos.append(f"Peso {peso}g fora do intervalo {PESO_MIN}-{PESO_MAX}g")

    if cor.lower().strip() not in CORES_VALIDAS:
        motivos.append(f"Cor '{cor}' inválida - permitido: {', '.join(CORES_VALIDAS)}")

    if not (COMPRIMENTO_MIN <= comprimento <= COMPRIMENTO_MAX):
        motivos.append(f"Comprimento {comprimento}cm fora do intervalo {COMPRIMENTO_MIN}-{COMPRIMENTO_MAX}cm")

    aprovada = len(motivos) == 0
    return aprovada, motivos

def recalcular_caixas():
    """Recalcula todas as caixas a partir da lista de peças aprovadas. Usado após remoção."""
    global caixas_fechadas, caixa_atual
    caixas_fechadas = []
    caixa_atual = []
    
    for peca in pecas_aprovadas:
        caixa_atual.append(peca)
        if len(caixa_atual) == CAPACIDADE_CAIXA:
            caixas_fechadas.append(caixa_atual.copy())
            caixa_atual = []

def cadastrar_peca():
    """Opção 1: Cadastra nova peça"""
    print("
--- CADASTRAR NOVA PEÇA ---")
    
    # Validação de ID
    id_peca = input("ID da peça: ").strip()
    if not id_peca:
        print("Erro: ID não pode ser vazio.")
        return
    
    # Verificar duplicidade
    todos_ids = [p['id'] for p in pecas_aprovadas + pecas_reprovadas]
    if id_peca in todos_ids:
        print(f"Erro: Já existe uma peça com ID '{id_peca}'.")
        return

    try:
        peso = float(input(f"Peso (g) [{PESO_MIN}-{PESO_MAX}]: ").replace(',', '.'))
        cor = input(f"Cor [{'/'.join(CORES_VALIDAS)}]: ").strip()
        comprimento = float(input(f"Comprimento (cm) [{COMPRIMENTO_MIN}-{COMPRIMENTO_MAX}]: ").replace(',', '.'))
    except ValueError:
        print("Erro: Peso e comprimento devem ser números.")
        return

    if not cor:
        print("Erro: Cor não pode ser vazia.")
        return

    aprovada, motivos = avaliar_peca(peso, cor, comprimento)

    peca = {
        "id": id_peca,
        "peso": peso,
        "cor": cor.lower(),
        "comprimento": comprimento
    }

    if aprovada:
        peca["status"] = "APROVADA"
        pecas_aprovadas.append(peca)
        caixa_atual.append(peca)

        print(f"
✅ Peça {id_peca} APROVADA!")

        # Verifica se precisa fechar caixa
        if len(caixa_atual) == CAPACIDADE_CAIXA:
            caixas_fechadas.append(caixa_atual.copy())
            print(f"📦 Caixa {len(caixas_fechadas)} FECHADA com {CAPACIDADE_CAIXA} peças!")
            caixa_atual.clear()
            print("📦 Nova caixa iniciada.")
        else:
            print(f"📦 Armazenada na caixa atual: {len(caixa_atual)}/{CAPACIDADE_CAIXA}")

    else:
        peca["status"] = "REPROVADA"
        peca["motivos"] = motivos
        pecas_reprovadas.append(peca)
        print(f"
❌ Peça {id_peca} REPROVADA!")
        for m in motivos:
            print(f"   - {m}")

def listar_pecas():
    """Opção 2: Lista aprovadas e reprovadas"""
    print("
--- LISTA DE PEÇAS ---")
    
    print(f"
✅ APROVADAS ({len(pecas_aprovadas)}):")
    if not pecas_aprovadas:
        print("   Nenhuma peça aprovada.")
    else:
        for p in pecas_aprovadas:
            print(f"   ID:{p['id']} | Peso:{p['peso']}g | Cor:{p['cor']} | Comp:{p['comprimento']}cm")

    print(f"
❌ REPROVADAS ({len(pecas_reprovadas)}):")
    if not pecas_reprovadas:
        print("   Nenhuma peça reprovada.")
    else:
        for p in pecas_reprovadas:
            print(f"   ID:{p['id']} | Peso:{p['peso']}g | Cor:{p['cor']} | Comp:{p['comprimento']}cm")
            for m in p['motivos']:
                print(f"      -> {m}")

def remover_peca():
    """Opção 3: Remove peça cadastrada"""
    print("
--- REMOVER PEÇA ---")
    if not pecas_aprovadas and not pecas_reprovadas:
        print("Nenhuma peça cadastrada.")
        return

    id_remover = input("Digite o ID da peça a remover: ").strip()
    
    # Tenta remover das aprovadas
    for i, p in enumerate(pecas_aprovadas):
        if p['id'] == id_remover:
            pecas_aprovadas.pop(i)
            recalcular_caixas()
            print(f"Peça {id_remover} removida das APROVADAS. Caixas recalculadas.")
            return

    # Tenta remover das reprovadas
    for i, p in enumerate(pecas_reprovadas):
        if p['id'] == id_remover:
            pecas_reprovadas.pop(i)
            print(f"Peça {id_remover} removida das REPROVADAS.")
            return

    print(f"Peça com ID '{id_remover}' não encontrada.")

def listar_caixas_fechadas():
    """Opção 4: Listar caixas fechadas"""
    print("
--- CAIXAS FECHADAS ---")
    print(f"Total de caixas fechadas: {len(caixas_fechadas)}")
    print(f"Peças na caixa atual (aberta): {len(caixa_atual)}/{CAPACIDADE_CAIXA}")
    
    if not caixas_fechadas:
        print("Nenhuma caixa fechada ainda.")
        if caixa_atual:
            print("
📦 Caixa atual (em uso):")
            for p in caixa_atual:
                print(f"   - ID:{p['id']}")
        return

    for idx, caixa in enumerate(caixas_fechadas, start=1):
        print(f"
📦 Caixa {idx} - {len(caixa)} peças:")
        for p in caixa:
            print(f"   - ID:{p['id']} | {p['peso']}g | {p['cor']} | {p['comprimento']}cm")

    if caixa_atual:
        print(f"
📦 Caixa {len(caixas_fechadas)+1} (ABERTA) - {len(caixa_atual)}/{CAPACIDADE_CAIXA}:")
        for p in caixa_atual:
            print(f"   - ID:{p['id']}")

def gerar_relatorio():
    """Opção 5: Relatório final consolidado"""
    print("
" + "="*50)
    print("RELATÓRIO FINAL - CONTROLE DE PRODUÇÃO")
    print("="*50)
    
    total_pecas = len(pecas_aprovadas) + len(pecas_reprovadas)
    total_caixas_utilizadas = len(caixas_fechadas) + (1 if caixa_atual else 0)

    print(f"Total de peças analisadas: {total_pecas}")
    print(f"Total aprovadas: {len(pecas_aprovadas)}")
    print(f"Total reprovadas: {len(pecas_reprovadas)}")
    print(f"Quantidade de caixas utilizadas: {total_caixas_utilizadas}")
    print(f"  - Caixas fechadas: {len(caixas_fechadas)}")
    print(f"  - Caixa em uso: {len(caixa_atual)}/{CAPACIDADE_CAIXA}")

    if pecas_reprovadas:
        print("
--- DETALHAMENTO DE REPROVAÇÕES ---")
        # Contar motivos
        from collections import Counter
        contagem_motivos = Counter()
        for p in pecas_reprovadas:
            for motivo in p['motivos']:
                # Simplifica para categoria
                if "Peso" in motivo:
                    contagem_motivos["Peso fora do padrão"] += 1
                elif "Cor" in motivo:
                    contagem_motivos["Cor inválida"] += 1
                elif "Comprimento" in motivo:
                    contagem_motivos["Comprimento fora do padrão"] += 1

        for motivo, qtd in contagem_motivos.items():
            print(f"  {motivo}: {qtd} ocorrência(s)")

        print("
Lista detalhada:")
        for p in pecas_reprovadas:
            print(f"  ID {p['id']}: {', '.join(p['motivos'])}")
    
    print("="*50)

def exibir_menu():
    print("
" + "="*50)
    print("SISTEMA DE GESTÃO DE PEÇAS - INDÚSTRIA")
    print("="*50)
    print("1. Cadastrar nova peça")
    print("2. Listar peças aprovadas/reprovadas")
    print("3. Remover peça cadastrada")
    print("4. Listar caixas fechadas")
    print("5. Gerar relatório final")
    print("0. Sair")
    print("="*50)

def main():
    print("Bem-vindo ao sistema de automação digital!")
    print(f"Critérios: Peso {PESO_MIN}-{PESO_MAX}g | Cores: {', '.join(CORES_VALIDAS)} | Comp: {COMPRIMENTO_MIN}-{COMPRIMENTO_MAX}cm")
    
    while True:
        exibir_menu()
        opcao = input("Escolha uma opção: ").strip()

        if opcao == "1":
            cadastrar_peca()
        elif opcao == "2":
            listar_pecas()
        elif opcao == "3":
            remover_peca()
        elif opcao == "4":
            listar_caixas_fechadas()
        elif opcao == "5":
            gerar_relatorio()
        elif opcao == "0":
            print("
Saindo do sistema. Gerando relatório final...")
            gerar_relatorio()
            print("Sistema encerrado!")
            break
        else:
            print("Opção inválida! Digite 0-5.")

if __name__ == "__main__":
    main()
