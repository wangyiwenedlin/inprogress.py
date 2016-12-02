# inprogress.py
from flask import *
from extension import mysql
from flask.ext.login import login_required, current_user

inprogress = Blueprint('inprogress', __name__)
@inprogress.route('/sidebar', methods=['GET','POST'])
def inprogress_route():

	cur = mysql.connection.cursor()
	
	cur.execute('SELECT * FROM MQCP WHERE released = 0 ')
	mqcp = cur.fetchall()

	mqcp_bust = list()
	for i in mqcp:
		mqcp_bust.append(i[0])

	cur.execute('SELECT * FROM ProcessCard WHERE released = 0  ')
	pc = cur.fetchall()

	pc_bust = list()
	for i in pc:
		pc_bust.append(i[0])

	cur.execute('SELECT * FROM RecordForm WHERE released = 0 ')
	rf = cur.fetchall()

	rf_bust = list()
	for i in rf:
		rf_bust.append(i[0])

	cur.execute('SELECT * FROM WorkInstruction WHERE released = 0 ')
	wi = cur.fetchall()

	wi_bust = list()
	for i in wi:
		wi_bust.append(i[0])

	cur.execute('SELECT * FROM WeldingRecord WHERE released = 0 ')
	wr = cur.fetchall()

	wr_bust = list()
	for i in wr:
		wr_bust.append(i[0])

	cur.execute('SELECT * FROM DimensionalRecord WHERE released = 0 ')
	dr = cur.fetchall()

	dr_bust = list()
	for i in dr:
		dr_bust.append(i[0])

	ids = list(set(mqcp_bust).union(set(pc_bust)))
	ids = list(set(ids).union(set(rf_bust)))
	ids = list(set(ids).union(set(wi_bust)))
	ids = list(set(ids).union(set(wr_bust)))
	ids = list(set(ids).union(set(dr_bust)))
	
	check = [([0] * 9) for i in ids]



	for i in ids:
		cur.execute('SELECT * FROM Product WHERE productid LIKE "%s";'% (i))
		pro = cur.fetchone()
		check[ids.index(i)][0] = pro[1]

		if i in mqcp_bust:
			check[ids.index(i)][1] = 1
		if i in pc_bust:
			check[ids.index(i)][2] = 1
		if i in rf_bust:
			check[ids.index(i)][3] = 1
		if i in wi_bust:
			check[ids.index(i)][4] = 1
		if i in wr_bust:
			check[ids.index(i)][5] = 1
		if i in dr_bust:
			check[ids.index(i)][6] = 1

		check[ids.index(i)][7] = i
		check[ids.index(i)][8] = pro[3]


	return jsonify(	CHECK = [{"pid":i[7], "pname": i[0], "mqcp_bust": i[1], "pc_bust": i[2], "rf_bust": i[3], "wi_bust": i[4], "wr_bust":i[5],"dr_bust":i[6], "creator": i[8]}  for i in check],
					USER = current_user.id
    	)


