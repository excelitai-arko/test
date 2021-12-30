<!-- ====================== -->
data-expire="{{Carbon\Carbon::parse($sale->sale_date)->format( 'Y/m/d h:m:s')}}"
<!-- ======= -->

<!-- ============for product cont condition -->
@if($product->count()>0 && $sale->status ==1 && $sale->sale_date > Carbon\Carbon::now())

@endif
<!-- ============end product count condition -->


<!-- ========================================================================= -->
$onsale=Order::select('products.*','brands.brand_name_cats_eye')->where('orders.created_at', '>',Carbon::now()->subHours(12)->toDateTimeString() )
        ->where('orders.status','=','delivered')
        ->join('order_items', 'order_items.order_id', '=', 'orders.id')
        ->join('products', 'products.id', '=', 'order_items.product_id')
        ->join('brands', 'brands.id', '=', 'products.brand_id')
        ->limit(8)
        ->get();
<!--=================================================================================  -->


<!-- ============================================for date function -->
printf("Right now is %s", Carbon::now()->toDateTimeString());
printf("Right now in Vancouver is %s", Carbon::now('America/Vancouver')); // automatically converted to string
$tomorrow = Carbon::now()->addDay();
$lastWeek = Carbon::now()->subWeek();

// Carbon embed 822 languages:
echo $tomorrow->locale('fr')->isoFormat('dddd, MMMM Do YYYY, h:mm');
echo $tomorrow->locale('ar')->isoFormat('dddd, MMMM Do YYYY, h:mm');

$officialDate = Carbon::now()->toRfc2822String();

$howOldAmI = Carbon::createFromDate(1975, 5, 21)->age;

$noonTodayLondonTime = Carbon::createFromTime(12, 0, 0, 'Europe/London');

$internetWillBlowUpOn = Carbon::create(2038, 01, 19, 3, 14, 7, 'GMT');

if (Carbon::now()->isWeekend()) {
    echo 'Party!';
}
echo Carbon::now()->subMinutes(2)->diffForHumans(); // '2 minutes ago'

// Want to know more about diffForHumans? Double-click on the method name!
<!-- =========================================================== -->




<!-- ======================== for png image design start-->
<img src="   {{ asset('backend/images/classy_new.png') }}" alt="logo" style="height: 60px; width: 240px; filter:invert(101%) sepia(49%) saturate(1191%) hue-rotate(262deg) brightness(150%) contrast(141%);">
<!-- ================================== end png image design part-->



<!-- ============================start=======================================three table joining with eloquent model start===========================================================================================================-->
<!-- for joining table we set foreign key(state_id) in city table which  key is  primary key in (state) table -->
<!-- for joining table we set foreign key(country_id) in (state) table which  key is  primary key in (country) table -->

table 1:city   (col1 (city_id)->primary_key, col2(state_id)->foreign_key,col3(city_name))

table 2:state   (col1 (state_id)->primary_key, col2(country_id)->foreign_key,col3(state_name))


table 3:country (col1 (country_id)->primary_key, col2(country_name))
<!-- ==================now we will get data from country table -->

<!-- ====model name==(county.php) -->
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Country extends Model
{
    use HasFactory;
    protected $table = 'country';
    protected $fillable = ['country_name'];

}

<!--  ==========end model code-->
<!--  ==========start Controller code===(JointableController)-->

<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\Models\Country;

class JointableController extends Controller
{
public function index()
  {
   //three table ( city, state,country,) joining code and get data from Country model

  $data = Country::join('state','state.country_id', '=', 'country.country_id')
                   ->('city','city.state_id', '=', 'state.state_id')
                   ->get(['country.country_name', 'state.state_name', 'city.city_name']);
return view ('join_table', compact('data'));

<!-- === ->get(['country.country_name', 'state.state_name', 'city.city_name']);  geting 3 column valu from 3 table with join == -->
<!--  ========= $data = Country::join('state','state.country_id', '=', 'country.country_id')  //this code join country table and state table here (state(table_name).country_id(foreign_key)) = (country(table_name).country_id(primary_key))========= -->

<!-- =======   ->('city','city.state_id', '=', 'state.state_id')  //this code join state table and city table here (city(table_name).state_id(foreign_key)) = (state(table_name).state_id(primary_key))======= -->
                
  }
}
<!--  ==========end Controller code-->

<!-- ========start view page===name ===join_table.blade.php === -->
  <table>
<thead>
<tr>
<th>country<th>
<th>state<th>
<th>city<th>

</tr>
<tbody>

@foreach($data as $row)
<tr>
<td>{{$row->country_name}}</td>
<td>{{$row->state_name}}</td>
<td>{{$row->city_name}}</td>

</tr>
@endforeach
</tbody>


</thead>

</table>
<!-- ========end view page===name ===join_table.blade.php === -->

<!-- =======start route code for view=== web.php=== -->
<?php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\JointableController

Route::get('join_tabel',[JointableController::class, 'index']);

<!-- =======end route code for view=== web.php=== -->




<!--==============================End=================================== three table joining with eloquent model and get data from country model==================================================================================================================-->
<!-- ==========for auto select district problem solve========= -->

 <script> 

$('.minus').click(function(){
if ($('#quantity').val() != 0)
$('#quantity' ).val(parseInt($('#quantity').val()) - 1);
});
$('.plus').click(function(){
$('#quantity').val(parseInt($('#quantity').val()) + 1);
});
</script>
{{-- select discrict and state --}}
<script type="text/javascript">
    $(document).ready(function() {
      $('select[name="division_id"]').on('change', function(){
          var division_id = $(this).val();
          if(division_id) {
              $.ajax({
                  url: "{{  url('/district-get/ajax') }}/"+division_id,
                  type:"GET",
                  dataType:"json",
                  success:function(data) {
                    $('select[name="state_id"]').empty();
                    $('select[name="district_id"]').empty();
                    console.log(data);
                    document.querySelector('#option_district_id .nice-select ul.list').innerHTML="";
                    let nice_select = $('select[name="district_id"] .nice-select')
                    let spanCurrent   = document.querySelector('#option_district_id .nice-select span.current');
                    let list          = document.querySelector('#option_district_id .nice-select ul.list');
                    spanCurrent.innerHTML = data[0].district_name;
                    $.each(data, function(key, value){
                        let liItem  = document.createElement('li');
                        liItem.setAttribute('data-value',value.id);
                        liItem.classList.add('option');
                        liItem.innerHTML=value.district_name;
                        list.append(liItem);
                        $('select[name="district_id"]').append('<option value="'+ value.id +'">' + value.district_name + '</option>');
                    });
                  },
              });
          } else {
              alert('danger');
          }
      });
      $('select[name="district_id"]').on('change', function(){
            var district_id = $(this).val();
            if(district_id) {
                $.ajax({
                    url: "{{  url('/state-get/ajax') }}/"+district_id,
                    type:"GET",
                    dataType:"json",
                    success:function(data) {
                       var d =$('select[name="state_id"]').empty();
                       document.querySelector('#option_state_id .nice-select ul.list').innerHTML="";
                        let nice_select = $('select[name="district_id"] .nice-select')
                        let spanCurrent   = document.querySelector('#option_state_id .nice-select span.current');
                        let list          = document.querySelector('#option_state_id .nice-select ul.list');
                        spanCurrent.innerHTML = data[0].state_name;
                          $.each(data, function(key, value){
                            let liItem  = document.createElement('li');
                            liItem.setAttribute('data-value',value.id);
                            liItem.classList.add('option');
                            liItem.innerHTML=value.state_name;
                            list.append(liItem);
                              $('select[name="state_id"]').append('<option value="'+ value.id +'">' + value.state_name + '</option>');
                          });
                    },
                });
            } else {
                alert('danger');
            }
        });
  });

</script>

<!-- ===========end auto select district in master blade=========== -->
  
<!-- =========================v-for============================== -->
 <div class="form-group">
                                        <div class="form-row">
                                            <div class="col-md-6">
                                                 <label for="exampleFormControlSelect1">Product Category</label>
                                            <select class="form-control" id="exampleFormControlSelect1" v-model="form.category_id">
                                                <option :value="category.id" :key="category.id" v-for="category in categories">{{category.category_name}}</option>

                                            </select>
                                            </div>
                                            <div class="col-md-6">
                                                    <label for="exampleFormControlSelect1">Product Supplier</label>
                                            <select class="form-control" id="exampleFormControlSelect1" v-model="form.supplier_id">
                                                <option :value="supplier.id" :key="index" v-for="(supplier,index) in suppliers">{{supplier.name}}</option>

                                            </select>
                                            </div>
                                        </div>
                                    </div>
<!-- =========================v-for==end============================ -->

<!-- ===============good paractice create function inside methods and call it out of method in created or mounted =============== -->

<script type="text/javascript">
export default {
    created() {
        if (!User.loggedIn()) {
            this.$router.push({ name: "/" });
        }
    },

    data() {
        return {
            form: {
                product_name: null,
                product_code: null,
                category_id: null,
                supplier_id: null,
                root: null,
                buying_price: null,
                selling_price: null,
                buying_date: null,
                image: null,
                product_quantity: null,
            },
            errors: {},
            categories : {},
            suppliers : {},
        }
    },
    methods: {
        onFileSelected(event){
            let file = event.target.files[0];
            if(file.size > 1048770){
                Notification.image_validation()
            }else{
                let reader = new FileReader();
                reader.onload = event =>{
                    this.form.image = event.target.result
                    console.log(event.target.result);
                };
            reader.readAsDataURL(file);
            }

        },
        employeeInsert(){
            axios.post('/api/employee', this.form)
            .then(() => {
                this.$router.push({ name: 'employee'})
                Notification.success()
            })
            .catch(error=>this.errors = error.response.data.errors)
        },
        getCategories(){
            axios.get('api/category/')
               .then(({data}) => (this.categories = data))
        },
        getSupplier(){
          axios.get('api/supplier/')
            .then(({data}) => (this.suppliers = data))
        }
    },
    created(){
       this.getCategories();
       this.getSupplier();
    },
}
</script>


<!-- ===============good paractice create function inside methods and call it out of method in created or mounted =============== -->
<!-- ==============auth user name insert by controller================ -->

public function create_discussion(Request $request){ 

    if(Auth::check()){
          $create = new ForumPost ; 
          $create->post_content = $request->content;
          $create->user_name = Auth::user()->name;
          $create->comment_frequency = 100;
          $create->user_id = Auth::user()->id;
          $create->save();

          return response()->json([
                'message' => 'Discussion created successfully '
          ]);
     } else {
         return response()->json([
                'message' => 'You are not authentic user. '
         ]);
    }
}



or you can use below code
========
=========
else
        {
            $message = new Message;
            if(Auth::guard('admin')->check()){
                $sender_name = Auth::guard('admin')->user()->name;
                dd($sender_name);
            }elseif(Auth::guard('super_admin')->check()){
                $sender_name = Auth::guard('super_admin')->user()->name;
            }elseif(Auth::guard('doctor')->check()){
                $sender_name = Auth::guard('doctor')->user()->name;
            }else{
                $sender_name = Auth::guard('employee')->user()->name;
            }
             //dd( $sender_name);
            $message->send_to = $request->input('sendto');
            $message->subject = $request->input('subject');
            $message->message = strip_tags($request->input('message'));
            $message->sender_name = $sender_name;
            $message->date = Carbon::now()->format('d-m-Y h:i:s a');
            $message->save();
            return response()->json([
                'status'=>200,
                'message'=>'Message Send Successfully',
            ]);
        }

==========================================

<!-- ==============auth user name insert by controller end================ -->
loop er modde id pass korle delet er somoi delete(`api/delete/${id}`)
delete button e click korle id ta auto generate..hide contents
<=====================message controller===============================>
use App\Models\Message;
use Carbon\Carbon;
use DateTime;
use DateTimeZone;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Validator;
class MessageController extends Controller
{
    public function SendMessageView(){
        $sender_name=$this->findUser();
        $messages= Message::where('sender_name',$sender_name)->get();
        //$messages= Message::where('sender_name','super admin')->get();
        //$messages= Message::where('sender_name','Doctor')->get();
        return view('super_admin.messages.send', compact('messages'));
    }
    public function InboxMessageView(){
        $sender_name=$this->findUser();
        $messages= Message::where('send_to', $sender_name)->get();
       // $messages= Message::where('send_to','Super-Admin')->get();
        //$messages= Message::where('send_to', 'Doctor')->get();
        return view('super_admin.messages.inbox', compact('messages'));
    }
    public function ShowSingleSentMessage($id){
        $message= Message::find($id);
        return response()->json(compact('message'));
    }
    public function ShowSingleInboxMessage($id){
        $message= Message::find($id);
        return response()->json(compact('message'));
    }
    public function SendMessageStore(Request $request){
        $validator = Validator::make($request->all(), [
            //'sendto'=> 'required|max:191',
            'subject'=> 'required|max:191',
            'message'=> 'required',
        ],[
            //'sendto.required' => 'Send To name is required',
            'subject.required' => 'Subject name is required',
            'message.required' => 'Message details is required',
        ]);
        if($validator->fails())
        {
            return response()->json([
                'status'=>400,
                'errors'=>$validator->messages(),
            ]);
        }
        else
        {
            $message = new Message;
            if(Auth::guard('admin')->check()){
                $sender_name = Auth::guard('admin')->user()->name;
            }elseif(Auth::guard('super_admin')->check()){
                $sender_name = Auth::guard('super_admin')->user()->name;
            }elseif(Auth::guard('doctor')->check()){
                $sender_name = Auth::guard('doctor')->user()->name;
            }else{
                $sender_name = Auth::guard('employee')->user()->name;
            }
             //dd( $sender_name);
            $message->send_to = $request->input('sendto');
            $message->subject = $request->input('subject');
            $message->message = strip_tags($request->input('message'));
            $message->sender_name = $sender_name;
            $message->date = Carbon::now()->format('d-m-Y h:i:s a');
            $message->save();
            return response()->json([
                'status'=>200,
                'message'=>'Message Send Successfully',
            ]);
        }
    }
    public function findUser(){
        if(Auth::guard('admin')->check()){
            $sender_name = Auth::guard('admin')->user()->name;
        }elseif(Auth::guard('super_admin')->check()){
            $sender_name = Auth::guard('super_admin')->user()->name;
        }elseif(Auth::guard('doctor')->check()){
            $sender_name = Auth::guard('doctor')->user()->name;
        }else{
            $sender_name = Auth::guard('employee')->user()->name;
        }
        return $sender_name;
        // dd($sender_name);
    }
    public function SendMessageDelete($id){
        $message = Message::findOrFail($id);
      Message::findOrFail($id)->delete();
      return redirect()->back();
    }
}
// $sender_name=Auth::user();
// $sender_name=Auth::guard('user')->check();


<=====================message controller end===============================>

