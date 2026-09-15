Automação Industrial - Gestão de Peças, Qualidade e Armazenamento
UniFECAF - Algoritmos e Lógica de Programação
Curso: Tecnologia em IA e Automação
Aluno: Traiffson Farllem França da Silva
RA: 275963
GitHub: @traiffson
Protótipo de controle de qualidade industrial desenvolvido em Python puro, executado no Thonny IDE.
🎯 Objetivo
Automatizar a inspeção manual na linha de produção, validando peso (95-105g), cor (azul/verde) e comprimento (10-20cm), com armazenamento inteligente em caixas de 10 unidades.
⚙️ Funcionalidades
Cadastro com ID, peso, cor e comprimento
Validação com múltiplos motivos de reprovação
Impede ID duplicado
Controle de caixas (fecha automático em 10)
Função recalcular_caixas() para reorganizar após remoção
Listagem de aprovadas/reprovadas, caixas fechadas e relatório final
🚀 Como Executar (Thonny)
Baixe automacao_industrial.py
Abra no Thonny e clique em Run UMA VEZ
Use o menu no Shell: 1=Cadastrar, 2=Listar, 3=Remover, 4=Caixas, 5=Relatório, 0=Sair
Código fonte está separado no arquivo .py
💡 Exemplos
P001 80g azul 15cm -> REPROVADA (peso)
P002 100g azul 15cm -> APROVADA 1/10
P004 vermelho -> REPROVADA (cor)
10ª aprovada -> 📦 Caixa 1 FECHADA!
2 + Enter -> lista tudo (a mágica)
🔗 Repositório
https://github.com/traiffson/automacao-industrial-unifecaf
