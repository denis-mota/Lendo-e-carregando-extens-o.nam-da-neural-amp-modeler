import customtkinter as ctk
from tkinter import filedialog
import json

# Função para carregar e ler o arquivo .nam
def carregar_arquivo_nam(caminho_arquivo):
    try:
        with open(caminho_arquivo, 'r') as arquivo:
            dados = json.load(arquivo)
            return dados
    except Exception as e:
        print(f"Erro ao ler o arquivo: {e}")
        return None

# Função para exibir a interface de FX Routing
def exibir_fx_routing(config_arquivo):
    if config_arquivo is None:
        print("Nenhum arquivo carregado.")
        return

    # Inicializando a janela principal
    root = ctk.CTk()
    root.title("FX Routing - Pedaleira")

    # Definindo a geometria e tornando a janela responsiva
    root.geometry("400x600")
    root.grid_rowconfigure(0, weight=1)
    root.grid_columnconfigure(0, weight=1)

    # Configurando a estrutura da tela
    frame_principal = ctk.CTkFrame(root)
    frame_principal.grid(row=0, column=0, padx=10, pady=10, sticky="nsew")

    # Exibindo informações sobre o amplificador
    arquitetura = config_arquivo.get('architecture', 'Desconhecida')
    ctk.CTkLabel(frame_principal, text=f"Arquitetura: {arquitetura}", font=("Arial", 10)).grid(row=0, column=0, pady=5)

    # Exibindo camadas (layers) com controles visuais
    layers = config_arquivo.get('config', {}).get('layers', [])
    
    for i, layer in enumerate(layers):
        ctk.CTkLabel(frame_principal, text=f"Camada {i+1}", font=("Arial", 10)).grid(row=i*6+1, column=0, pady=3)

        # Tamanho de entrada (input size)
        ctk.CTkLabel(frame_principal, text=f"Entrada: {layer.get('input_size')}", font=("Arial", 8)).grid(row=i*6+2, column=0, sticky="w")
        
        # Tamanho da Condição (condition size)
        ctk.CTkLabel(frame_principal, text=f"Cond.: {layer.get('condition_size')}", font=("Arial", 8)).grid(row=i*6+3, column=0, sticky="w")
        
        # Canais (channels) - Controle deslizante (Slider)
        slider_channels = ctk.CTkSlider(frame_principal, from_=1, to=128, width=200)
        slider_channels.set(layer.get('channels', 8))  # Definindo o valor inicial
        slider_channels.grid(row=i*6+4, column=0, sticky="w", pady=5)

    # Adicionando os controles de distorção e equalização
    ctk.CTkLabel(frame_principal, text="Controle de Distorção", font=("Arial", 10)).grid(row=len(layers)*6+1, column=0, pady=5)
    
    # Controle de Distorção
    slider_distortion = ctk.CTkSlider(frame_principal, from_=0, to=10, width=200)
    slider_distortion.set(5)  # Valor inicial para a distorção
    slider_distortion.grid(row=len(layers)*6+2, column=0, pady=5)

    # Filtros de Equalização: Bass, Mid, High, Presence e Volume de Saída
    ctk.CTkLabel(frame_principal, text="Equalização", font=("Arial", 10)).grid(row=len(layers)*6+3, column=0, pady=5)

    # Controle de Bass
    slider_bass = ctk.CTkSlider(frame_principal, from_=-10, to=10, width=200)
    slider_bass.set(0)  # Valor inicial para Bass
    slider_bass.grid(row=len(layers)*6+4, column=0, pady=5)

    # Controle de Mid
    slider_mid = ctk.CTkSlider(frame_principal, from_=-10, to=10, width=200)
    slider_mid.set(0)  # Valor inicial para Mid
    slider_mid.grid(row=len(layers)*6+5, column=0, pady=5)

    # Controle de High
    slider_high = ctk.CTkSlider(frame_principal, from_=-10, to=10, width=200)
    slider_high.set(0)  # Valor inicial para High
    slider_high.grid(row=len(layers)*6+6, column=0, pady=5)

    # Controle de Presence
    slider_presence = ctk.CTkSlider(frame_principal, from_=-10, to=10, width=200)
    slider_presence.set(0)  # Valor inicial para Presence
    slider_presence.grid(row=len(layers)*6+7, column=0, pady=5)

    # Controle de Volume de Saída
    slider_volume = ctk.CTkSlider(frame_principal, from_=0, to=10, width=200)
    slider_volume.set(5)  # Valor inicial para Volume
    slider_volume.grid(row=len(layers)*6+8, column=0, pady=5)

    # Botão para finalizar e aplicar as alterações
    def aplicar_configuracao():
        print("Aplicando configurações...")
        for i, layer in enumerate(layers):
            canais = slider_channels.get()
            print(f"Camada {i+1} - Canais ajustados para: {canais}")
        
        distorcao = slider_distortion.get()
        print(f"Distorção ajustada para: {distorcao}")
        
        bass = slider_bass.get()
        print(f"Bass ajustado para: {bass}")
        
        mid = slider_mid.get()
        print(f"Mid ajustado para: {mid}")
        
        high = slider_high.get()
        print(f"High ajustado para: {high}")
        
        presence = slider_presence.get()
        print(f"Presence ajustado para: {presence}")
        
        volume = slider_volume.get()
        print(f"Volume de Saída ajustado para: {volume}")
        
        root.quit()

    botao_aplicar = ctk.CTkButton(root, text="Aplicar Configuração", command=aplicar_configuracao)
    botao_aplicar.grid(row=len(layers)*6+9, column=0, pady=10)

    # Iniciar a interface gráfica
    root.mainloop()

# Função para abrir a janela de seleção de arquivo
def selecionar_arquivo_nam():
    root = ctk.CTk()
    root.withdraw()  # Não queremos que a janela principal do Tkinter apareça
    caminho_arquivo = filedialog.askopenfilename(
        title="Selecione o arquivo .nam",
        filetypes=[("Arquivos .nam", "*.nam"), ("Todos os arquivos", "*.*")]
    )
    return caminho_arquivo

# Função principal que orquestra a leitura e exibição da interface
def main():
    # Abrir a interface gráfica para selecionar o arquivo .nam
    caminho_arquivo_nam = selecionar_arquivo_nam()
    
    if caminho_arquivo_nam:
        print(f"Arquivo selecionado: {caminho_arquivo_nam}")
        # Carregar os dados do arquivo .nam
        config_arquivo = carregar_arquivo_nam(caminho_arquivo_nam)

        # Exibir a interface de FX Routing com os dados carregados
        exibir_fx_routing(config_arquivo)
    else:
        print("Nenhum arquivo foi selecionado.")

# Executar a função principal
if __name__ == "__main__":
    main()

######################################################
#foi usado metodo de leitura json_load() e wavenet ###
######################################################
