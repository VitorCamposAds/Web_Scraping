# Web_Scraping
#This script uses the Selenium, BeautifulSoup and other libraries to automate the process of searching and scraping in google.
import requests
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.chrome.options import Options #define as opções para o navegador
from selenium.webdriver.common.by import By
import pyautogui
import time
import xlwt

pyautogui.alert("O processo de automatização vai começar, portanto, não utilize nada do computador até o script ser finalizado!")

options = Options()
#options.add_argument('window-size=400,600')
#options.add_argument('--headless') #argumento que esconde o navegador


navegador = webdriver.Chrome(options=options)
navegador.get("https://www.google.com.br/")
time.sleep(2)
#pular = navegador.find_element(By.CSS_SELECTOR, "buttom >img")
#if pular:
    #pular.click()

#Coleta os dados da 1 página
input_place = navegador.find_element(By.TAG_NAME, 'input') #encontra a classe da busca
input_place.send_keys("Troca de Óleo Belo Horizonte") #escreve na busca
input_place.submit() #da um 'enter' na busca
time.sleep(2)
navegador.find_element(By.CSS_SELECTOR, "span.wUrVib.OSrXXb[aria-level='3'][role='heading']").click()
html_content = navegador.page_source
soup = BeautifulSoup(html_content, 'html.parser')
print(soup.prettify())
dados_1_pag = []

for dado in soup.findAll(['a', 'div']):
    dados_1_pag.append(dado.text)
dados_1_pag_string = '\n'.join(dados_1_pag)
dados_1_pag_soup = BeautifulSoup(dados_1_pag_string, 'html.parser')
print(dados_1_pag_soup.prettify())

dados_gerais = dados_1_pag

for i in range(2, 12):
    page_link = navegador.find_element(By.CSS_SELECTOR, f'a.fl[aria-label="Page {i}"]')
    page_link.click()
    time.sleep(3)
    html_content = navegador.page_source
    soup = BeautifulSoup(html_content, 'html.parser')
    dados_pag = []
    for dado in soup.findAll(['a', 'div']):
        dados_pag.append(dado.text)
    dados_gerais.extend(dados_pag)

# Criando o arquivo Excel
workbook = xlwt.Workbook()
sheet = workbook.add_sheet("dados")

# Escrevendo os dados na planilha
for i, dado in enumerate(dados_gerais):
    sheet.write(i, 0, dado)

# Salvando o arquivo
workbook.save("dados.xls")


input("Enter para sair")
