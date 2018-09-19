# xiami_music
- 爬取虾米音乐排行榜上的音源

- ```python
  import requests
  
  from selenium import webdriver
  from selenium.webdriver.support import expected_conditions as ec
  from selenium.webdriver.common.by import By
  from selenium.webdriver.support.wait import WebDriverWait
  from lxml import etree
  from kaisha import str2url
  
  browser = webdriver.Chrome()
  wait = WebDriverWait(browser, 10)
  
  
  def get_html():
      # 获取html代码
      browser.get('https://www.xiami.com/chart/index/c/103/type/0?spm=a1z1s.2943549.6827461.1.a26OKv')
      wait.until(ec.presence_of_element_located((By.CSS_SELECTOR, '.songwrapper')))
     	page_source = browser.page_source
     	return page_source
  
  
  def get_mp3(html):
     	page = etree.HTML(html)
     	urls = page.xpath('//*[@id="chart"]/table/tr/@data-mp3')
     	names = page.xpath('//*[@id="chart"]/table/tr/td[4]/div/div[2]/p[1]/strong/a/text()')
     	for x in range(len(names)):
       	url = str2url(urls[x])
        	filename = './music/' + names[x] + '.mp3'
        		with open(filename, 'bw') as f:
           		f.write(requests.get(url).content)
           		print(filename, 'Done!')
  
    	browser.close()
  
  
  def main():
     	get_mp3(get_html())
  
  
  if __name__ == '__main__':
     main()
  ```
