# jogo-da-cobrinha-em-python
import pygame
from pygame.locals import *
from sys import exit 
from random import randint 
from pygame import mixer
pygame.init() 


#parametros jogo
largura, altura = 660, 480 #660 em x e 480 em y
relogio = pygame.time.Clock()#quanto maior a quantidade de fraimes mais rapido o jogo roda
fim_jogo = False


#parametros de cores em RGB
#preta = (0, 0, 0)
#branca = (255, 255, 255)
#vermelha = (255, 0, 0)
#verde = (0, 255, 0)


#parametros musica
pygame.mixer.music.set_volume(0.2)
fundo_musica = mixer.music.load('Snake Baron.mp3')
mixer.music.play(-1)
musica_colidir = pygame.mixer.Sound('smw_coin.wav')


# Parâmetros da cobra
#dividimos por 2 a largura e altura,  pois vai aparcer no meio da tela
x_cobrinha = largura // 2
y_cobrinha = altura // 2
comprimento_inicial = 2
pontuacao = 0
lista_cobra = []


# Parâmetros da comida
#com a funçao RANDOM aciona outros valores(numeros aleatorios no intervalo de 30 e 480 no x)
#usamos um valor em x e em y
x_comida = randint(30, 480)
y_comida = randint(50, 400)


# Controlando a velocidade dos botões
velocidade_cobra = 10
x_velocidade = velocidade_cobra
y_velocidade = 0
velocidade_atual = velocidade_cobra


#tela de inico
game_over = False
inicio = True
iniciar_jogo = False


#tela padrão, com as suas dimensões
tela = pygame.display.set_mode((largura, altura))#a funcão setmode recebe na a largura e altura como dimensoes da tela
pygame.display.set_caption('Jogo da cobrinha')#a funçao set_caption nos permite colocar na nome na janela(titulo do jogo)


#uma funçao para inicio do jogo
def iniciar_jogo():
    global inicio
    inicio = False


#aqui vamos usar as letras do teclado para a movimentação, velocidade da cobra, que são(para cima:UP, para baixo:DOWN, para esquerda:LEFT e para direita:Right)
def controle_velocidade(tecla, x_velocidade, y_velocidade):
    if tecla == K_LEFT and x_velocidade == 0:           #se a cobra não está se movendo horizontalmente (velocidade horizontal é zero)
        x_velocidade = -velocidade_cobra
        y_velocidade = 0                                #para a cobrar anda so na horizontal
    elif tecla == K_RIGHT and x_velocidade == 0:
        x_velocidade = velocidade_cobra
        y_velocidade = 0
    elif tecla == K_UP and y_velocidade == 0:
        x_velocidade = 0
        y_velocidade = -velocidade_cobra
    elif tecla == K_DOWN and y_velocidade == 0:
        x_velocidade = 0
        y_velocidade = velocidade_cobra

    return x_velocidade, y_velocidade


def tela_inicio():
    tela.fill((0,0,0))
    tela.fill((0, 255, 0))
    fonte3 = pygame.font.SysFont('Helvetica', 25)
    mensagem_inicio = 'Pressione ESPAÇO para iniciar'
    texto_inicio = fonte3.render(mensagem_inicio, True, (0, 0, 0))
    ret_texto_inicio = texto_inicio.get_rect()
    ret_texto_inicio.center = (largura // 2, altura // 2)
    tela.blit(texto_inicio, ret_texto_inicio)
    pygame.display.update()


#função para reinicio do jogo assim que o player tocar na propria cobra.
def restart_no_jogo():
    global pontuacao, comprimento_inicial, x_cobrinha, y_cobrinha, lista_cobra, lista_head, x_comida, y_comida, fim_jogo, x_velocidade, y_velocidade, velocidade_atual
    pontuacao = 0
    comprimento_inicial = 4
    x_cobrinha = largura / 2
    y_cobrinha = altura / 2
    lista_cobra = []
    lista_head = []
    x_comida = randint(30, 480)
    y_comida = randint(50, 400)
    fim_jogo = False
    velocidade_cobra = 10
    x_velocidade = velocidade_cobra
    y_velocidade = 0
    velocidade_atual = velocidade_cobra
    


def desenhar_pontuacao(pontuacao):
    fonte = pygame.font.SysFont('Helvetica', 25)
    texto = fonte.render(f'Pontos: {pontuacao}', True, (255,0,0))
    tela.blit(texto, (10, 10))

def comprimento_cobra(lista_cobra):
    for XeY in lista_cobra:# as variaveis XeY representam uma [x,y], XeY[0] = x e [1]=y
        pygame.draw.rect(tela, (0, 255, 0), (XeY[0], XeY[1], 20, 20))


#loop principal do jogo 
while True:
    relogio.tick(velocidade_atual)
    #relogio.tick(8)
    tela.fill((0, 0, 0))

    for event in pygame.event.get():
        if event.type == QUIT:
            pygame.quit()
            exit()
            #função que foi definida no inicio com sys import exit
        elif event.type == KEYDOWN:
            x_velocidade, y_velocidade = controle_velocidade(event.key, x_velocidade, y_velocidade)
        if event.type == KEYDOWN:
            if inicio and event.key == K_SPACE:
                iniciar_jogo()
    

    if inicio:
        tela_inicio()
    else:
           

    #aqui vai parar de se mover nas diagonais
        x_cobrinha += x_velocidade
        y_cobrinha += y_velocidade

   

        tamanho_cobra = pygame.draw.rect(tela, (0, 255, 0), (x_cobrinha, y_cobrinha, 20, 20))#retangulo que representa a cor verde da cobra, 330 em x e 240 em y e 20 de largura e 20 de altura
        tamanho_comida = pygame.draw.rect(tela, (255, 0, 0), (x_comida, y_comida, 20, 20))#retangulo que representa a cor vermelha da comida


  
        if tamanho_cobra.colliderect(tamanho_comida):
            x_comida = randint(30, 480)
            y_comida = randint(50, 400)
            pontuacao += 1
            comprimento_inicial += 1
            velocidade_atual += 1#toda vez que a cobra colide com a comida ela aumenta de velocidade, conforme aumenta de tamanho
            musica_colidir.play()

   
    
        lista_head = []#armazena os valores de x e y da cabeça da cobra
        lista_head.append(x_cobrinha)#aqui armazena os valores de x
        lista_head.append(y_cobrinha)#aqui armazena os valores de y
        lista_cobra.append(lista_head)


  
        if lista_cobra.count(lista_head) > 1:
            fonte2 = pygame.font.SysFont('Helvetica', 15, True, True)
            mensagem = 'Você morreu! Clique no botão esquerdo do mouse para jogar novamente'
            texto_formatado = fonte2.render(mensagem, True, (0,0,0))
            ret_texto = texto_formatado.get_rect()
            fim_jogo = True
            while fim_jogo:
                tela.fill((0,255,0))
                for event in pygame.event.get():
                    if event.type == QUIT:
                        pygame.quit()
                        exit()
                    if event.type == MOUSEBUTTONDOWN:
                        if event.button == 1:
                            restart_no_jogo()
                            
                ret_texto.center = (largura//2, altura//2) 
                tela.blit(texto_formatado, ret_texto)
                pygame.display.update()


    
        
        if x_cobrinha > largura or x_cobrinha < 0 or y_cobrinha < 0 or y_cobrinha > altura:
            restart_no_jogo()
           

    
        if len(lista_cobra) > comprimento_inicial:
            del lista_cobra[0]
        

        comprimento_cobra(lista_cobra)


        desenhar_pontuacao(pontuacao)
    

   
 
    pygame.display.update()

   
