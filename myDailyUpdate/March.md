

# MARCH


## 11-03-2024 -- Image Api

### Made  Image Post api  which uploads , compressed  and save's images using flask api , Pillow and flask-restful 


## 12-03-2024 - Json to Excel Convertor Api
### Implemented  function which convertor json data into excel via using openpyxl and Excelwriter 
#### Code
```python
import pandas as pd 
import re
from openpyxl.styles import Font

def excel_generator(json_data):
        
    try:
        orders = json_data["data"]
        
        # get order name by iterating the order and reg for removing special char
        order_by_names = [re.sub(r'[ ,]', '', order["order_by"]["name"]) for order in orders]
        
        # Concatenate order_by_names into the Excel file name
        excel_file_name = "combined_orders_" + "_".join(order_by_names) + ".xlsx"
        excel_file_path = "excelUploads/" + excel_file_name
        
        # Initialize a counter for the starting row
        start_row = 1

        # Create an Excel writer
        with pd.ExcelWriter(excel_file_path, engine='openpyxl') as writer:
            
            # Initialize a list to store r_ids
            r_ids = []
            
            # Iterate over each order
            for order in orders:
                r_id = order["rid"]
                r_ids.append(r_id)
                order_id = order["orderId"]
                items = order["items"]
                status = order["status"]
                order_by_name = order["order_by"]["name"]

                # Increment the start row for the next order
                start_row += 2 
                
                # Initialize lists to store extracted values
                item_ids = []
                prices = []
                quantities = []
                statuses = []
                sum_totals = []

                # Extract data for current order
                for item in items:
                    item_id = item["itemId"]
                    price = float(item["price"])
                    quantity = int(item["qty"])
                    free = item["free"]
                    cd = item["cash_disc"]
                    
                    # Append extracted values to lists
                    item_ids.append(item_id)
                    prices.append(price)
                    quantities.append(quantity)
                    statuses.append(status)
                    sum_totals.append(price * quantity)

                # Create a DataFrame for the current order
                font = Font(bold=True, size=17)
                df = pd.DataFrame({
                    "Item ID": item_ids,
                    "Price": prices,
                    "Quantity": quantities,
                    "Free": [free] * len(item_ids),
                    "CD": cd,
                    "Total_Sum": sum_totals,
                })

                # Write DataFrame to Excel with headers
                df_headers_r_id = pd.DataFrame({"R_ID": [r_id]}, index=[0])
                df_headers_r_id.to_excel(writer, index=False, startrow=start_row, startcol=3)

                cell = df_headers_order_name = pd.DataFrame({"Ordered By Name": [order_by_name]}, index=[0])
                cell.font = font
                df_headers_order_name.to_excel(writer, index=False, startrow=0, startcol=2)
                
                df_headers_order_id = pd.DataFrame({"Order_ID": [order_id]}, index=[0])
                df_headers_order_id.to_excel(writer, index=False, startrow=start_row, startcol=2)
                df.to_excel(writer, index=False, startrow=start_row, startcol=4, header=True)

                # Increment the start row for the next order
                start_row += len(df) + 4  # Add space of 2 rows between orders

            print("Successfully created Excel file:", excel_file_name)
            return excel_file_name
        
    except Exception as e:
        print(e)
        raise e

```

## 13-03-2024 - Implemented Excel Convertor Api
###  optimizated the api  and converted into According to the format's  and update's some attributes in excel file via  using openpyxl and pandas library 


## 14-03-2024 - Implemented Excel Convert Api 
### Blinded excel  convertor functions into api route  as well as via api using normal html page and modify excel files attributes using pandas and openpyxl 
#### Code 

##### 
```python
@app.route('/excel-converter/', methods=['POST'])
def excel_converter():
    try:
        data = request.json
        date = data['date'] 
        rId = data['rId'] 
        payload = {"rId":  rId, "date": date}
        
        url = "http://143.244.136.36:8000/api/orderTaking/all-orders-items/order-by"
        
        response = requests.get(url, json=payload)
        
        if response.status_code == 200:
            # Call excel_generator function to create Excel file
            excel_file_name = excel_generator(response.json())
            print( excel_file_name)
            
            # Send the Excel file as a response
            return jsonify({"file_url": "excelUploads/" + excel_file_name, "message": "Convert successfully"})
        else:
            return jsonify({'error': 'Failed to fetch data from the URL'}), response.status_code
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/excel-converter/<filename>')
def download_excel(filename):
    try:
        # Specify the path to the Excel file
        excel_path = "excelUploads/" + filename

        # Send the file to the client for download
        return send_file(excel_path, as_attachment=True)

    except Exception as e:
        return str(e), 500

``` 

## 15-03-2024 - Configrued Mongodb Atlas 
###  Configrued Mongodb Atlas  with flask api for new project  and test and debug wheather api and mongodb working propering or not

### Code
```python
app = Flask(__name__,template_folder='templates')

app.config['MONGO_DBNAME'] = 'Users'
app.config["MONGO_URI"] = "mongodb://localhost:27017/Users"
app.config["SECRET_KEY"]= 'Qu6jMMgHVBttelF1fmj4hftwQKVm7fBIAA'

mongo = PyMongo(app)
```

## 18-03-2023 - Not Present 


## 19-03-2023 - Working with mongodb and UI 
###  
#### Made form UI form using html,css  
##### Code
```css
#-----fill form-------

.text-box,.text2-box {
    /* padding: 10px 10px;
    border-radius: 9px;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px; */
    padding-left: 10px !important;
}

.text-box{
  padding-left: 10px;
  width: 100%;
  padding: 10px;
  margin: 3px;
}
input::file-selector-button {
  font-weight: bold;
  color: #ffff;
  padding: 0.5em;
  border: none;
  background:  #f4ff1e95;
    
}
.text-box,input:focus-visible{
  border: none;
  border-color: white;
  border-radius: 30px;
}
/* .text-box:hover{
  background: re;
} */
.text-box label{

  padding-top: 10px;
  padding-bottom: 10px;
  font-size: 16px;
 width: 100%;
 text-align: center;
 

}
.text-box input{
  padding: 20px;
  
}
.both-div {
    /* padding: 10px 10px; */
    border-radius: 9px;
    transition: 0.5s all ;
    
    display: flex;
    border-radius: 1px thin yellow;
    /* align-items: center; */
    height: 100%;
}
.both-div:hover{
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
  padding-top: 5px;
  transform: translate(7px,7px);
  /* transform: rotateY(150deg); */
 
}


.icon-box {
    background-color: #eaea39;
    /* width: 30px; */
    color: black;
    /* height: 80px; */
    display: flex;
    justify-content: center;
    align-items: center;
    padding-left: 7px;
    border-top-left-radius: 8px;
    border-bottom-left-radius: 8px;
}
@import 'https://fonts.googleapis.com/css?family=Roboto:400,700';
    @import 'https://code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css';

    .clearfix::before,
    .clearfix::after {
      display: table;
      content: "";
    }

    .clearfix::after {
      clear: both;
    }
    .submit-mag
    {
      background-color: #eaea39 !important;
      color: black !important;
      padding: 3px 5px !important;
    }

    .sub-note {
         display: flex !important;
         gap: 35px !important;
         justify-content: center !important;
        }
    .card {
      border: 1px solid #e5e5e5;
      border-radius: 8px;
      /* box-shadow: 0 7px 8px rgba(0, 0, 0, 0.15); */
      display: inline-block;
      font-family: "Roboto", sans-serif;
      margin: 20px;
      position: relative;
      vertical-align: top;
      /* width: 300px; */
    }

    .card::after {
      background: url("https://s3.amazonaws.com/uploads.hipchat.com/11887/1259435/Fs6cXOPe83o7Rd0/agsquare_%402X.png");
      bottom: 0;
      content: "";
      left: 0;
      opacity: 0.4;
      position: absolute;
      right: 0;
      top: 0;
      z-index: -1;
    }

    .main-content {
      padding: 15px 15px 0;
      border-radius: 8px;
    }

    .status-label {
      border-radius: 2px;
      color: #f5f5f5;
      display: inline-block;
      font-size: 12px;
      padding: 5px 10px;
      margin-bottom: 9px;
      text-transform: uppercase;
    }

    .label-bar {
      display: flex;
      justify-content: end;
      align-items: end;
    }

    .positive .status-label,
    .positive .sub-note {
      background: #e5e508cc;
      color: black;
      /* box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px; */
    }

    .neutral .status-label,
    .neutral .sub-note {
      background: #E9AE36;
      color: black;
    }

    .negative .status-label,
    .negative .sub-note {
      background: #E7504E;
    }

    .checkbox-paira {
      color: black;
    }
    .fa-regular.fa-file{
      padding-right: 8px;
    }
    .card-title {
      font-weight: 700;
      padding: 7px 0;
      margin: 3px;
      font-size: 20px;
    }

    [class*=ion-]:before {
      font-size: 18px;
      left: -30px;
      margin-top: -9px;
      position: absolute;
      top: 50%;
    }

    .info-listing {
      font-size: 15px;
      /* margin: 0 0 0 30px; */
    }
    dt.dt-class{
      width: 100% !important;
      border-bottom: 1px solid rgb(221, 219, 219) !important;
    }

    .info-listing dt,
    .info-listing dd {
      border-bottom: 1px solid #e5e5e5;
      display: inline-block;
      float: left;
      padding: 15px 0;
      position: relative;
      width: 50%;
    }

    .info-listing dt:last-of-type,
    .info-listing dd:last-of-type {
      border-bottom: none;
    }

    .info-listing dt {
      color: #777777;
    }

    .info-listing dd {
      color: #B5BEC5;
      margin: 0;
      text-align: right;
    }

    .sub-note {
      color: #f5f5f5;
      font-size: 14px;
      line-height: 135%;
      padding: 3px;
    margin-top: 17px;
    margin-bottom: 17px;
    }

    .submit-btn {
  margin-bottom: 1rem;
  margin-left: 20px;
  border-radius: 50px;
  border: 2px solid #eaea39;
  background-color: transparent;
  font-size: 14px;
  padding: 6px 13px 7px 13px;
  font-weight: 500;
    }
    .submit-btn a{
      color: black !important ;
      text-decoration: none;
    }
    .c-title{
      color: black;
      font-size: 17px;
      display: flex;
      gap: 9px;
    }
    .s-title{
      color: #777777;
    }
.checkbox-paira{
  color: black;
}
    .sub-title {
      font-size: 12px;
    }

    .sub-note {
      display: flex;
      gap: 13px;
    }

    .kabel-img {
      position: relative;
      left: 10px;
    }
    .date-time{
      margin: 0px !important;
  border: 1px solid #00000029;
  border-radius: 3px;
  padding: 3px 3px;
  width: fit-content;
  position: absolute;
  top: -12px;
  background-color: #000000a3;
  color: white;
    }

    .custom-submit{
      background-color: #eaea39;
      /* border: none; */
    }

  ```

  ```python

    result = list(mongo.db.users.aggregate(pipeline))
    user_sum =  mongo.db.users.count_documents({})
    doc_sum = mongo.db.document.count_documents({})

    return render_template("dashboard.html",data=result,active_index=1,userdata=loged_in_user_data(ObjectId(current_user.id)) ,user_count  = user_sum,doc_sum = doc_sum)
```
## 20-03-2024 --  Worked on Frontend Using Html css javascript
### Made one form Userinterface and adjust and designed the button 
#### Code :-
```html
{% include "header.html" %}

            <!-- <h3 class="section-header">Welcome to Transaction </h3> -->


            <div class="bottom-container">




                <div class="bottom-container__left">
                    <div class="box transaction-box">
                        <div class="user-count">
                            {% if userdata.roll == 'manager' %}
                            <div class="user-count-value">
                                <h3 class="total_cou">Total Users</h3>
                                <h3 class="total_no">{{ user_count }}</h3>
                            </div>
                            <div class="pending-doc">
                                <h3 class="total_cou">Total Documents</h3>  
                                <a href="/addDoc">add document</a><br>  
                                <h3 class="total_no">{{ doc_sum }}</h3>
                            </div>
                            {% else %}
                            <div class="user-count-value">
                                <h3 class="total_cou">Total Documents</h3>
                                <h3 class="total_no">{{ counts }}</h3> <!-- Assuming this is the total documents count for the user -->
                            </div>
                            <div class="user-count-value">
                                <h3 class="total_cou">Submitted Documents</h3>
                                <h3 class="total_no">{{ submit }}</h3> <!-- Assuming this is the count of submitted documents for the user -->
                            </div>
                            {% endif %}
                        </div>
                    </div>


                    <!-- table -->

                    {% if userdata.roll == 'manager' %}
                    <div class="box transaction-box">
                        
                        <div class="form-group"> 	
                            <div class  ="form-control" id="maxRows" >
                                <h3 class="cards-heading cards2-heading">Users</h3>
                               <div class="searchbox2"> <div action="#" class="search">
                                <svg class="search__icon" width="22" height="22" viewBox="0 0 22 22" fill="none"
                                    xmlns="http://www.w3.org/2000/svg">
                                    <path
                                        d="M10.5418 19.25C15.3513 19.25 19.2502 15.3512 19.2502 10.5417C19.2502 5.73223 15.3513 1.83337 10.5418 1.83337C5.73235 1.83337 1.8335 5.73223 1.8335 10.5417C1.8335 15.3512 5.73235 19.25 10.5418 19.25Z"
                                        stroke="#596780" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" />
                                    <path d="M20.1668 20.1667L18.3335 18.3334" stroke="#596780" stroke-width="1.5"
                                        stroke-linecap="round" stroke-linejoin="round" />
                                </svg>
                                <input type="text" id="search_input_all" onkeyup="FilterkeyWord_all_table()" class="search__input" placeholder="Search something here">
                            </div></div>
                                 
            </div>
                            
                         </div>      
                      

                        <table class="table" id="customers">
                            <thead>
                            <tr>
                              <th>user</th>
                              <th>pending</th>
                              <th>requested</th>
                              <th>submitted</th>
                            </tr>
                            </thead>
                            <tbody>
                                {% for a in data %}  <!-- Check if an error message exists -->
                            
                                <tr>
                                    <td>{{ a._id }}</td>
                                    <td>{{ a.count_pending }}</td>
                                    <td>{{ a.count_required }}</td>
                                    <td>{{ a.count_submitted }}</td>                             
                                </tr>
                        
                            {% endfor %}
                            </tbody>
                          </table>
                                
                          <div class='pagination-container'>
                            <nav>
                                <ul class="pagination">
                                
                                </ul>
                            </nav>
                        </div>
                    </div>
                    
                    {% endif %}                
                   
                </div>
              

        </main>
    </div>
```

```javascript
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>

    <script>  
    getPagination('#customers');
        $('#maxRows').trigger('change');
        function getPagination (table){
    
              $('#maxRows').on('change',function(){
                  $('.pagination').html('');						// reset pagination div
                  var trnum = 0 ;									// reset tr counter 
                  var maxRows = 25;			// get Max Rows from select option
            
                  var totalRows = $(table+' tbody tr').length;		// numbers of rows 
                 $(table+' tr:gt(0)').each(function(){			// each TR in  table and not the header
                     trnum++;									// Start Counter 
                     if (trnum > maxRows ){						// if tr number gt maxRows
                         
                         $(this).hide();							// fade it out 
                     }if (trnum <= maxRows ){$(this).show();}// else fade in Important in case if it ..
                 });											//  was fade out to fade it in 
                 if (totalRows > maxRows){						// if tr total rows gt max rows option
                     var pagenum = Math.ceil(totalRows/maxRows);	// ceil total(rows/maxrows) to get ..  
                                                                 //	numbers of pages 
                     for (var i = 1; i <= pagenum ;){			// for each page append pagination li 
                     $('.pagination').append('<a data-page="'+i+'">\
                                          <span>'+ i++ +'<span class="sr-only">(current)</span></span>\
                                        </a>').show();
                     }											// end for i 
         
             
                } 												// end if row count > max rows
                $('.pagination a:first-child').addClass('active'); // add active class to the first li 
            
            
            //SHOWING ROWS NUMBER OUT OF TOTAL DEFAULT
           showig_rows_count(maxRows, 1, totalRows);
            //SHOWING ROWS NUMBER OUT OF TOTAL DEFAULT
    
            $('.pagination a').on('click',function(e){		// on click each page
            e.preventDefault();
                    var pageNum = $(this).attr('data-page');	// get it's number
                    var trIndex = 0 ;							// reset tr counter
                    $('.pagination a').removeClass('active');	// remove active class from all li 
                    $(this).addClass('active');					// add active class to the clicked 
            
            
            //SHOWING ROWS NUMBER OUT OF TOTAL
           showig_rows_count(maxRows, pageNum, totalRows);
            //SHOWING ROWS NUMBER OUT OF TOTAL
            
            
            
                     $(table+' tr:gt(0)').each(function(){		// each tr in table not the header
                         trIndex++;								// tr index counter 
                         // if tr index gt maxRows*pageNum or lt maxRows*pageNum-maxRows fade if out
                         if (trIndex > (maxRows*pageNum) || trIndex <= ((maxRows*pageNum)-maxRows)){
                             $(this).hide();		
                         }else {$(this).show();} 				//else fade in 
                     }); 										// end of for each tr in table
                        });										// end of on click pagination list
            });
                                                // end of on select change 
             
                                    // END OF PAGINATION 
        
        }	
    
    
                
    
    // SI SETTING
    $(function(){
        // Just to append id number for each row  
    default_index();
                        
    });
    
    //ROWS SHOWING FUNCTION
    function showig_rows_count(maxRows, pageNum, totalRows) {
       //Default rows showing
            var end_index = maxRows*pageNum;
            var start_index = ((maxRows*pageNum)- maxRows) + parseFloat(1);
            var string = 'Showing '+ start_index + ' to ' + end_index +' of ' + totalRows + ' entries';               
            $('.rows_count').html(string);
    }
    
    // CREATING INDEX
    function default_index() {
      $('table tr:eq(0)').prepend('<th> No </th>')
    
                        var id = 0;
    
                        $('table tr:gt(0)').each(function(){	
                            id++
                            $(this).prepend('<td>'+id+'</td>');
                        });
    } 
    
    // All Table search script
    function FilterkeyWord_all_table() {
      
    // Count td if you want to search on all table instead of specific column
    
      var count = $('.table').children('tbody').children('tr:first-child').children('td').length; 
    
            // Declare variables
      var input, filter, table, tr, td, i;
      input = document.getElementById("search_input_all");
      var input_value =     document.getElementById("search_input_all").value;
            filter = input.value.toLowerCase();
      if(input_value !=''){
            table = document.getElementById("customers");
            tr = table.getElementsByTagName("tr");
    
            // Loop through all table rows, and hide those who don't match the search query
            for (i = 1; i < tr.length; i++) {
              
              var flag = 0;
               
              for(j = 0; j < count; j++){
                td = tr[i].getElementsByTagName("td")[j];
                if (td) {
                 
                    var td_text = td.innerHTML;  
                    if (td.innerHTML.toLowerCase().indexOf(filter) > -1) {
                    //var td_text = td.innerHTML;  
                    //td.innerHTML = 'shaban';
                      flag = 1;
                    } else {
                      //DO NOTHING
                    }
                  }
                }
              if(flag==1){
                         tr[i].style.display = "";
              }else {
                 tr[i].style.display = "none";
              }
            }
        }else {
          //RESET TABLE
          $('#maxRows').trigger('change');
        }
    }</script>
</body>

</html>

```

## 21-03-2024 - Worked on Flask Api 
### Modify's the Flask API so widgets of form can create and rendered properly
#### Code :-

```python
@app.route('/pending',methods=['GET'])
@login_required
def pendingDoc():
    user_id = ObjectId(current_user.id)
    pipeline = [
        {
            '$match': {
                '_id': user_id,
                'doc.status': 'pending'
            }
        },
        {
            '$project': {
                '_id': 0,
                'matching_doc': {
                    '$filter': {
                        'input': '$doc',
                        'as': 'item',
                        'cond': {'$eq': ['$$item.status', 'pending']}
                    }
                }
            }
        }
    ]
     

    doccu=list(mongo.db.users.aggregate(pipeline))
    if not doccu:
        doccu=['0']
    return render_template("pending.html",data=doccu[0],active_index=3,userdata=loged_in_user_data(ObjectId(current_user.id)))

@app.route('/submitted',methods=['GET'])
@login_required
def submitted():
    user_id = ObjectId(current_user.id)
    user_details=loged_in_user_data(ObjectId(current_user.id))
    if user_details['roll']=='user':
        pipeline = [
            {
                '$match': {
                    '_id': user_id,
                    'doc.status': 'submitted'
                }
            },
            {
                '$project': {
                    '_id': 0,
                    'matching_doc': {
                        '$filter': {
                            'input': '$doc',
                            'as': 'item',
                            'cond': {'$eq': ['$$item.status', 'submitted']}
                        }
                    }
                }
            }
        ]
    else:
        pipeline = [
            {
                '$match': {
                    'doc.status': 'submitted'
                }
            },
            {
                '$project': {
                    'name': '$name',
                    'matching_doc': {
                        '$filter': {
                            'input': '$doc',
                            'as': 'item',
                            'cond': {'$eq': ['$$item.status', 'submitted']}
                        }
                    }
                }
            }
        ]
     

    doccu=list(mongo.db.users.aggregate(pipeline))
    if not doccu:
        doccu=['0']

    return render_template("submitted.html",data=doccu,active_index=2,userdata=user_details)

```

## 22-03-2024  - Worked MongoDB and UI 
### Modifys the mongodb to stored form attributes implemented:- add checkbox ,made from UI to Stored  and creats form ,modifys the flask api to stored form data in mongodb acccordingly 
#### Code :- 
```python
@app.route('/fill_data/<string:object_id>', methods=['GET','POST'])
@login_required
def fill_data(object_id):
    
    if request.method == 'POST':
        obj_id = ObjectId(object_id)
        query = {'_id': ObjectId(current_user.id), 'doc._id': obj_id}
        current_time =datetime.datetime.now(india_timezone)
        formatted_time = current_time.strftime("%d %b %Y %I:%M%p")
        for field_name, file in request.files.items():
            filename = secure_filename(file.filename)
            file.save(os.path.join('static/uploads/documents', filename))
            update={'$set': {'doc.$.status': 'submitted','doc.$.submitedOn':formatted_time,'doc.$.'+field_name:filename}}
            mongo.db.users.update_one(query, update)
        for field_name in request.form:
            update={'$set': {'doc.$.status': 'submitted','doc.$.submitedOn':formatted_time,'doc.$.'+field_name:request.form.get(field_name)}}
            mongo.db.users.update_one(query, update)
        return redirect('/pending')
    else:
        obj_id = ObjectId(object_id)
        data = mongo.db.document.find_one({'_id': obj_id})
        print(data)
        data1 = dict({'data':data,'check' : 1})
        print("this is data one",data1)
        
        return render_template("fillDoc.html",data=data,active_index=3,userdata=loged_in_user_data(ObjectId(current_user.id)),check =1)
```
```html
{% include "header.html" %}

<div class="bottom-container">


    <div class="bottom-container__left">
     
        <div class="bottom-container">




            <div class="bottom-container__left">


                <div class="box transaction-box">
                    <div class="header-container">
                        <h3 class="section-header">Posted Documents</h3>

                    </div>
                    <div class="card-section">

                        <div class="card2">
                            <div class="card-sections">

                                <div class="cards-more_details">
                                    <form method="POST" enctype="multipart/form-data">
                                        <section class="card positive">
                                                <div class="main-content">
                                                    {% if data.onboard %}
                                                  <div class="label-bar">                                                   
                                                    <div class="status-label">Onboard</div>
                                                  </div>
                                            {% endif %}
                                                  <h5 class="date-time"> {{ data.createdOn }}</h5>
                                            
                                            
                                                  <div class="card-title">{{ data.docName }}</div>
                                                  <div class="card-title sub-title">{{ data.note }}</div>
                                                  <dl class="info-listing clearfix" style="padding-top: 8px;">
                                    {% for i in range(1, data.maxCountItem+1) %}
                                        {% if data['text'+ i | string] %}
                                        
                                            <div class="both-div">
                                                <div class="icon-box">
                                                  <i class="fa-regular fa-file" style="  font-size: 18px;
                                                padding-right: 7px; color: gray;"></i>
                                                  <i class="fa fa-file-text-o" aria-hidden="true"></i>
                                                </div>
                                                <div class="text-box">
                                                  <label for=""> {{ data['text'+ i | string] }}</label><br>
                                                  <input type="text" name="{{ 'filled-text'+ i | string }}" style="  border-radius: 6px;
                                                  border: 1px solid gray;
                                                  height: 30px;
                                                  margin-top: 5px;
                                                  margin-bottom: 5px;">
                                                </div>
                                              </div><br>
                                            {% endif %}

                                            {% if data['file'+ i | string] %}                                        

                                            <div class="both-div">
                                                <div class="icon-box">
                                                  <i class="fa-regular fa-file" style="  font-size: 18px;
                                                padding-right: 7px; color: gray;"></i>
                                                  <i class="fa fa-file-text-o" aria-hidden="true"></i>
                                                </div>
                                                <div class="text-box">
                                                  <label for=""> {{ data['file'+ i | string] }}</label><br>
                                                  <input type="file" name="{{ 'filled-file'+ i | string }}" style="  padding-right: 7px; color: gray;">
                                                </div>
                                              </div> 
                                              <br>
                                            {% endif %}

                                            {% if data['checkbox'+ i | string] %}                                        

                                            <div class="both-div">
                                                <div class="icon-box">
                                                  <i class="fa-regular fa-file" style="  font-size: 18px;
                                                padding-right: 7px; color: gray;"></i>
                                                  <i class="fa fa-file-text-o" aria-hidden="true"></i>
                                                </div>
                                                <div class="text-box">
                                                  <label for=""> {{ data['checkbox'+ i | string] }}</label><br>
                                                  <input type="checkbox" name="{{ 'filled-checkbox'+ i | string }}" style="  padding-right: 7px; color: gray;">
                                                </div>
                                              </div> 
                                              <br>
                                            {% endif %}
                                        {% endfor %}
                                                   
                                                    <br>                                                                                            
                                                  </dl>
                                                </div>
                                            
                                                <button class="submit-btn custom-submit">Submit</button>
                                              </section>
                                </form>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>

       

        </div>


        </main>
    </div>

   
    </body>

    </html>
```

```python
@app.route('/fill_data/<string:object_id>', methods=['GET','POST'])
@login_required
def fill_data(object_id):
    
    if request.method == 'POST':
        obj_id = ObjectId(object_id)
        query = {'_id': ObjectId(current_user.id), 'doc._id': obj_id}
        current_time =datetime.datetime.now(india_timezone)
        formatted_time = current_time.strftime("%d %b %Y %I:%M%p")
        for field_name, file in request.files.items():
            filename = secure_filename(file.filename)
            file.save(os.path.join('static/uploads/documents', filename))
            update={'$set': {'doc.$.status': 'submitted','doc.$.submitedOn':formatted_time,'doc.$.'+field_name:filename}}
            mongo.db.users.update_one(query, update)
        for field_name in request.form:
            update={'$set': {'doc.$.status': 'submitted','doc.$.submitedOn':formatted_time,'doc.$.'+field_name:request.form.get(field_name)}}
            mongo.db.users.update_one(query, update)
        return redirect('/pending')
    else:
        obj_id = ObjectId(object_id)
        data = mongo.db.document.find_one({'_id': obj_id})
        print(data)
        data1 = dict({'data':data,'check' : 1})
        print("this is data one",data1)
        
        return render_template("fillDoc.html",data=data,active_index=3,userdata=loged_in_user_data(ObjectId(current_user.id)),check =1)
```



## 26-03-2024
### Working on Image uploads api using flask and AWS s3 
```python 

@app.route('/upload', methods=['POST'])
def upload_image():
    try:
        if request.method == "POST":
            
            rid = request.form.get("rId")
            if 'file' not in request.files:
                return jsonify({'error': 'No file part'})

            file = request.files['file']

            if file.filename == '':
                return jsonify({'error': 'No selected file'})

            if file and allowed_file(file.filename):
                compressed_img = compress_image(file)

                try:
                    if compressed_img:
                        s3 = boto3.client(
                            service_name="s3",
                            aws_access_key_id=os.getenv('AWS_ACCESS_KEY_ID'),
                            aws_secret_access_key=os.getenv('AWS_SECRET_ACCESS_KEY')
                        )
                        # Upload BytesIO object directly to S3
                        
                        #set file Name with datetime and Filename and rid 
                        filename_extension = file.filename.split(".")[1]
                        current_datetime = datetime.datetime.now()
                        formatted_datetime = current_datetime.strftime("%m%d%Y%H%M%S")
                        date = str(formatted_datetime)
                   
                        print("the files extension",filename_extension)
                        file_name =  date + "_" + str(rid)  + "." +  str(filename_extension)
                        s3.upload_fileobj( compressed_img, Bucket="gifts-photos", Key=file_name)
                    
                        return jsonify({
                            "filename":file_name,
                            "code" : 200
                        })
                except Exception as e:
                    return jsonify({'error': f'Error uploading file to S3: {str(e)}'})
            else:
                return jsonify({'error': 'File type not allowed'})

    except Exception as e:
        return jsonify({'error': str(e)})

    return jsonify({"message": "File is successfully downloaded"})

  ```




## 27-03-2024 - Worked on Excel convertor 
### Working on Excel to json convertor using openpyxls and flask api  
```python 
from collections import defaultdict
from flask_cors import CORS
import pandas as pd 
from openpyxl.styles import Font
from utils import *
import io
import pandas as pd
from openpyxl import Workbook
from openpyxl.styles import Font
from openpyxl.styles import Font, PatternFill

def excel_generator(json_data):
    try:
        orders = json_data
        # Create a new Excel workbook
        workbook = Workbook()

        workbook.remove(workbook.active)
        # Create a worksheet
        worksheet = workbook.create_sheet("Orders")
        # Initialize a counter for the starting row
        start_row = 1
        # Iterate over each order
        for order in orders:
            r_id = order["rid"]
            order_id = order["orderId"]
            items = order["items"]
            r_name  = order["r_info"]["r_name"]
            order_name  = order["order_by"]["name"]
            order_date = order["order_at"]
            item_ids = []
            prices = []
            quantities = []                                                                                            # Initialize lists to store extracted values
            sum_totals = []
            sum_free = 0 
            sum_of_cd = []
    
            # Extract data for current order
            for item in items:
                item_id = item["itemId"]
                price = float(item["price"])
                quantity = int(item["qty"])
                free = item["free"]
                cd = int(item["cash_disc"])
            
                # Append extracted values to lists
                item_ids.append(item_id)
                prices.append(price)
                quantities.append(quantity)
                sum_totals.append(price * quantity)
                sum_of_cd.append(cd)

            # Write headers
            item_summary = defaultdict(lambda: {'quantity': 0, 'total_sum': 0})
            headers = ["Product", "Price", "Quantity", "Free", "CD", "Total_Sum"]
            for col_idx, header in enumerate(headers, start=1):
                worksheet.cell(row=start_row, column=col_idx + 4, value=header).font = Font(bold=True)

            # Write data for the current order
            for row, item_data in enumerate(zip(item_ids, prices, quantities, [free]*len(item_ids), [cd]*len(item_ids), sum_totals), start=start_row + 1):
                for col_idx, value in enumerate(item_data, start=1):
                    worksheet.cell(row=row, column=col_idx + 4, value=value)
            
            
            # Add row for Order ID and R_ID
            
            worksheet.cell(row=start_row , column = 1, value="Billed to" )
            worksheet.cell(row=start_row , column=2, value=   str(r_name) + "("  + str(r_id)+ ")" )   
            worksheet.cell(row=start_row+1 , column = 1, value="Ordered By" ) 
            worksheet.cell(row=start_row+1 , column = 2, value= order_name ) 
            worksheet.cell(row=start_row+2 , column = 1, value="Order Id" ) 
            worksheet.cell(row=start_row+2 , column = 2, value= order_id ) 
            
            # Increment the start row for the next order
            worksheet.cell(row=start_row + len(quantities) +1, column = 7, value=  sum(quantities)).font = Font(bold=True) 
            worksheet.cell(row=start_row + len(quantities) + 1, column=8, value=sum_free).font = Font(bold=True)
            worksheet.cell(row=start_row + len(quantities) + 1, column=9, value=sum(sum_of_cd)).font = Font(bold=True)
            worksheet.cell(row=start_row + len(quantities) + 1, column=10, value=sum(sum_totals)).font = Font(bold=True)
         
            green_fill = PatternFill(start_color="00FF00", end_color="00FF00", fill_type="solid")
            worksheet.cell(row=start_row + len(quantities) + 1, column=7).fill = green_fill
            worksheet.cell(row=start_row + len(quantities) + 1, column=8).fill = green_fill
            worksheet.cell(row=start_row + len(quantities) + 1, column=9).fill = green_fill
            worksheet.cell(row=start_row + len(quantities) + 1, column=10).fill = green_fill
            start_row += len(items) + 4  # Add space of 2 rows between orders
            
            
        item_summary = {}
    
        
            # sheet2.cell(row=idx, column=9, value=summary['total_sum'])          #uncomment this line once's price set
        excel_data = io.BytesIO()
             
        workbook.save(excel_data)

        return excel_data.getvalue()

    except Exception as e:
        print(e)
        raise e

```


## 28-03-2024 - Worked on Excel Convertor and there alignment's
### Aligned the excel as per requirements and made summary via openpyxl and workbook 
```python 
    sheet2 = workbook.create_sheet("summary")                                       #Create a Workbook for summary
        # Iterate over each order
        for order in orders:
            items = order["items"]
            for item in items:
                item_id = item["itemId"]
                price = float(item["price"])
                quantity = int(item["qty"])
                free = int(item["free"])
                cd  = int(item["cash_disc"])
                free = int(item["free"])

                # Update item summary
                if item_id not in item_summary:
                    item_summary[item_id] = {'Quantity': quantity, 'Total_sum': price * quantity,"Free":free,'Cd':cd}
                else:
                    item_summary[item_id]['Quantity'] += quantity
                    item_summary[item_id]['Total_sum'] += price * quantity
                    item_summary[item_id]['Free'] += free
                    item_summary[item_id]['Cd']  += cd
            # Save the workbook to BytesIO object
       
        start_rows = 2
        
        
        sheet2.cell(row=start_row , column = 1, value="Billed to" )
        sheet2.cell(row=start_row , column=2, value=   str(r_name) + "("  + str(r_id)+ ")" )   
        sheet2.cell(row=start_row+1 , column = 1, value="Ordered By" ) 
        sheet2.cell(row=start_row+1 , column = 2, value= order_name ) 
        sheet2.cell(row=start_row+2 , column = 1, value="Order Id" ) 
        sheet2.cell(row=start_row+2 , column = 2, value= order_id ) 
        sheet2.cell(row=start_row+2 , column = 1, value="Order Date" ) 
        sheet2.cell(row=start_row+2 , column = 2, value= order_date ) 
        # Write headers for item summary
        headers = ["Product"," ", "Quantity", " ","Free"," ","Cd"]
        for col_idx, header in enumerate(headers, start=1):
            sheet2.cell(row=start_rows, column=col_idx , value=header).font = Font(bold=True)
            sheet2.cell(row=start_rows, column=col_idx+1, value=" ").font = Font(bold=True)

        # Write item summary
        total_quantity = 0
        total_free = 0
        cash_discount = 0
        for idx, (item_id, summary) in enumerate(item_summary.items(), start=start_rows + 1):
            sheet2.cell(row=idx, column=1, value=item_id)
            sheet2.cell(row=idx, column=3, value=summary['Quantity'])
            sheet2.cell(row=idx, column=5, value=summary['Free'])
            sheet2.cell(row=idx, column=7, value=summary['Cd'])
            total_quantity += summary['Quantity']
            total_free += summary["Free"]
            cash_discount  += summary["Cd"]
         
        sheet2.cell(row=len(item_summary) + 4, column=1, value="Total Quantity").font = Font(bold=True)
        sheet2.cell(row=len(item_summary) + 5, column=1, value=total_quantity)
        
        sheet2.cell(row=len(item_summary) + 4, column=4, value="Total Free ").font = Font(bold=True)
        sheet2.cell(row = len(item_summary)+ 5 ,column=4,value  = total_free )
        
        sheet2.cell(row=len(item_summary) + 4, column=6, value="Total Cash Discount ").font = Font(bold=True)
        sheet2.cell(row = len(item_summary)+ 5 ,column=6,value  = cash_discount )
     
```


## 29-03-2024 - Worked on Frontend part 
### fetch data from flask and render's on html templates 
``` javascript 
  var flaskData = {{ data|tojson|safe }};
        var statusAll = {{ order_state|tojson|safe }};
        console.log("all data",flaskData)

        ////
        
        let total_order = document.querySelector(".total-orders")
        console.log(total_order)
        let total_order_count  = 0;

        ////
        let payer_id = document.querySelector(".payer-id") ;
        let payerIds = new Set() ;

        ////
        let orders_retailors = document.querySelector(".order_retailor") ;
        let order_retailor = 0 ;
        
        ////

        let no_orders = document.querySelector(".no-orders");
        let no_orders_count = new Set() ;
        let order_with_out_items =  0 ;
        let order_with_items = 0 ;

        ////

        for (const data of flaskData) {

            if(data.r_info && data.r_info.payerId){

                    payerIds.add(data.r_info.payerId);
            
            }
            if(data.r_info && data.r_info.r_name){

                order_retailor++ ;
            }
            
            if (data.items && Array.isArray(data.items)) {  
                
                if(data.items.length === 0){
                    order_with_out_items ++ ;
                }
                order_with_items++  ;
                for (const item of data.items) {
                       
                    if (item.qty) {
                      
                        total_order_count += parseInt(item.qty);
                    }
                }
            }
        }

        // Total orders count 
        total_order.innerHTML  += total_order_count


        //Total's Payer Id  count 
        console.log(payer_id)
        payer_id.innerHTML  += payerIds.size
        
        //Total No Order Count's
        no_orders.innerHTML +=  order_with_out_items

        // Total No of Order Retailors 
        orders_retailors.innerHTML += order_retailor
```