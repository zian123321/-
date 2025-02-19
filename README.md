import requests
from bs4 import BeautifulSoup
import json

class UserRegistration:
    def register(self, data):
        headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4472.117 Safari/537.36'}
        data = {'reg': data['reg'], 'username': data['username']}
        
        response = requests.post('https://user.register.com', headers=headers, json=data)
        if response.status_code == 200:
            print("注册成功！")
        else:
            raise ValueError(f"注册失败，状态码：{response.status_code}")

    def register_all(self):
        reg_table = self.getREGTable()
        for reg in reg_table['data']:
            data = {
                'reg': reg['reg'],
                'username': reg['username']
            }
            response = requests.post('https://user.register.com', headers=self.headers, json=data)
            if response.status_code == 200:
                print("添加注册记录成功！")
    def user_change(self, data):
        # 将数据作为字典，例如{"username": "new_name", ...}
        self.saveReg(data)
        
class RegTable:
    def __init__(self):
        self.data = []
    
    def getREGTable(self):
        import time
        count = 0
        while True:
            try:
                response = requests.get('https://user.register.com', headers=self.headers)
                soup = BeautifulSoup(response.text, 'html.parser')
                tags = soup.find_all('div', {'class': 'reg-list'})
                for t in tags:
                    data = {
                        'reg': t.find('span').text,
                        'username': t.find('a').text
                    }
                    self.data.append(data)
                    count += 1
                    if count > 50:
                        break
            except requests.exceptions.RequestException as e:
                print(f"请求次数超过限制，跳转到 reg-list")
                time.sleep(2)
        return self.data

if __name__ == '__main__':
    user_reg = UserRegistration()
    user_reg.register_all('注册表单')
    
class PaymentSystem:
    def handle_payment(self, method, amount):
        # 等待支付
        while True:
            try:
                response = requests.post(f"https://api.{method}.weChat.com/2ndflow/transfer?code={self RegTable().getRegData()}")
                data = json.loads(response.text)
                if data.get('response') == 'transferred':
                    return amount
                else:
                    time.sleep(1)
            except requests.exceptions.RequestException as e:
                print(f"请求失败，等待时间：{time.time()}秒")
                continue
