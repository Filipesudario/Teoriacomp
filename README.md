import sys
import json
import csv
import time

def carregar_automato(arquivo_aut):
    with open(arquivo_aut, 'r') as f:
        dados = json.load(f)
    inicial = dados["initial"]
    finais = set(dados["final"])
    transicoes = {}
    for trans in dados["transitions"]:
        origem = trans["from"]
        leitura = trans["read"]
        destino = trans["to"]
        if origem not in transicoes:
            transicoes[origem] = {}
        transicoes[origem][leitura] = destino
    return inicial, finais, transicoes

def processar_palavra(estado_inicial, estados_finais, transicoes, palavra):
    estado_atual = estado_inicial
    for simbolo in palavra:
        if estado_atual not in transicoes or simbolo not in transicoes[estado_atual]:
            return 0
        estado_atual = transicoes[estado_atual][simbolo]
    return 1 if estado_atual in estados_finais else 0

def executar_simulador(arquivo_aut, arquivo_in, arquivo_out):
    inicial, finais, transicoes = carregar_automato(arquivo_aut)
    resultados = []

    with open(arquivo_in, 'r') as f_in:
        leitor = csv.reader(f_in, delimiter=';')
        for linha in leitor:
            palavra, esperado = linha
            inicio = time.time()
            obtido = processar_palavra(inicial, finais, transicoes, palavra)
            fim = time.time()
            duracao = f"{(fim - inicio):.3f}"
            resultados.append([palavra, esperado, obtido, duracao])

    with open(arquivo_out, 'w', newline='') as f_out:
        escritor = csv.writer(f_out, delimiter=';')
        escritor.writerows(resultados)

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Uso: python simulador.py <automato.aut> <entradas.in> <saida.out>")
        sys.exit(1)
    executar_simulador(sys.argv[1], sys.argv[2], sys.argv[3])
    print('Hello world!')
