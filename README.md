# xiami_music
- 爬取虾米音乐排行榜上的音源

- ```python
  import requests
  
  from selenium import webdriver
  from selenium.webdriver.support import expected_conditions as ec
  from selenium.webdriver.common.by import By
  from selenium.webdriver.support.wait import WebDriverWait
  from lxml import etree
  
  browser = webdriver.Chrome()
  wait = WebDriverWait(browser, 10)
  
  
  def get_html():
      # 获取html代码
      browser.get('https://www.xiami.com/chart/index/c/103/type/0?spm=a1z1s.2943549.6827461.1.a26OKv')
      wait.until(ec.presence_of_element_located((By.CSS_SELECTOR, '.songwrapper')))
      page_source = browser.page_source
      return page_source
  
  
  def str2url(s):
      # 虾米因为是采用凯撒算法进行加密的，此方法激昂凯撒密码解密，能直接得到音源的地址
      #s = '9hFaF2FF%_Et%m4F4%538t2i%795E%3pF.265E85.%fnF9742Em33e162_36pA.t6661983%x%6%%74%2i2%22735'
      num_loc = s.find('h')
      rows = int(s[0:num_loc])
      strlen = len(s) - num_loc
      cols = int(strlen/rows)
      right_rows = strlen % rows
      new_s = list(s[num_loc:])
      output = ''
      for i in range(len(new_s)):
          x = i % rows
          y = i / rows
          p = 0
          if x <= right_rows:
              p = x * (cols + 1) + y
          else:
              p = right_rows * (cols + 1) + (x - right_rows) * cols + y
          output += new_s[int(p)]
      return parse.unquote(output).replace('^', '0')
  
  
  def get_mp3(html):
      page = etree.HTML(html)
      urls = page.xpath('//*[@id="chart"]/table/tr/@data-mp3')
      names = page.xpath('//*[@id="chart"]/table/tr/td[4]/div/div[2]/p[1]/strong/a/text()')
      for x in range(len(names)):
          url = str2url(urls[x])
          filename = './music/' + names[x] + '.mp3'
          with open(filename, 'bw') as f:
              f.write(requests.get(url).content)
              print(names[x], 'Done!')
      
      browser.close()
  
  
  def main():
      get_mp3(get_html())
  
  
  if __name__ == '__main__':
       main()
  ```
