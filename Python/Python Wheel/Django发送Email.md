``` python
# Email  django HTML页面邮件发送

import datetime
import os, django    
from django.comf import settings
from django.core.mail import EmailMultiAlternatives
from django.template import loader

os.environ.setdefault('DJANGO_SETTINGS_MODULEL', 'pa_sds.setting')
djngo.setup()

def send_mial(id_job):
    html_date = dict()


    title = ''  #主题
    reciptent_list = ['7634..'] #收件人

    email_template_name = 'email.html'
    info = loader.get_template(email_template_name)
    html_content = info.render(html_date)
    msg = EmailMultiAlternatives(
        title,
        html_content,
        settings.Email_FROM,
        reciptent_list,
        )
    mag.attach_alternative(html_content, 'text/html')
    mag.send()




#时间时差：  (create_date+datetime.timedelta(hours=8)).strftime("%Y-%m-%d %H:%M:%S")

```

