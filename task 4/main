import numpy as np
import pandas as pd
import io,os
import matplotlib.pyplot as plt
import seaborn as sns

import sqlite3
import pandas as pd
from sqlalchemy import create_engine

from PIL import Image
from flask import Flask,make_response,send_file,jsonify,request,render_template

app=Flask(__name__,template_folder=os.getcwd())

df=pd.read_csv('covid19india.csv')
df.head()
type(df)

def Deceased_graph(df,qstate,qfrom_age,qto_age,qgender,qfrom_date,qto_date):
    engine1 = create_engine('sqlite://',echo=False)
    df.to_sql('covid1',engine1, if_exists='replace',index=False)
    run_line1 = "Select count(patientId),reportedOn,ageEstimate from covid1 where state='"+qstate+"' and gender='"+qgender+"' and status='Deceased' group by reportedOn"
    
    results=engine1.execute(run_line1)
    final1 =pd.DataFrame(results)
    if final1.empty:
        return False
    else:
        final1.columns=['count','reported','age']
    
    engine2 = create_engine('sqlite://',echo=False)
    final1.to_sql('covid2',engine2, if_exists='replace',index=False)
    run_line2 = "Select count,reported from covid2 where age between "+qfrom_age+" and "+qto_age+" and reported between '"+qfrom_date+"' and '"+qto_date+"'"

    results2 = engine2.execute(run_line2)
    final2   = pd.DataFrame(results2)
    if final2.empty:
        return False
    else:
        print('y')
        final2.columns=['count','reported']
       
        final2['reported'] = pd.to_datetime(final2['reported'])
        final2 = final2.sort_values(by="reported")

        print(final2)
        plt.subplots(figsize=(10,7))
        plt.plot(final2['reported'],final2['count'],marker="o",
                 markerfacecolor="k",color="red",linewidth=2,linestyle="--",label='Deceased Patients')

        plt.title('Deceased Patients Graph',fontsize=20)
        plt.xlabel('Date',fontsize=20)
        plt.ylabel('Deceased patients',fontsize=20)
        plt.xticks(rotation=90)

        plt.savefig('./static/img.jpg')
    return True

def valid_request(state,from_age,to_age,gender,from_date,to_date):
    if state and from_age and to_age and gender and from_date and to_date:
        return True
    return False

@app.route('/getplot',methods=['GET'])
def getplot():
    state=request.args.get('state')
    from_age=request.args.get('fromage')
    to_age=request.args.get('toage')
    gender=request.args.get('gender')
    from_date=request.args.get('fromdate')
    to_date=request.args.get('todate')

    if valid_request(state,from_age,to_age,gender,from_date,to_date):
        if Deceased_graph(df,state,from_age,to_age,gender,from_date,to_date):
            return render_template("index.html", image = "./static/img.jpg")
    return make_response(jsonify({"response":"No Deceased case found"})),400

if __name__=="__main__":
    app.run(debug=True)


