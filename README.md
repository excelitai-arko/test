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
<=================start========>
strip_tags($request->description) use for remove tag from texeditor table.
<=================start end========>



<<<==============================employe=====================controller============================add========start========>>>
<?php
namespace App\Http\Controllers\Employee;
use App\Http\Controllers\Controller;
use App\Models\Account_Info;
use App\Models\AddProject;
use App\Models\Department;
use Illuminate\Http\Request;
use App\Models\EducationAndTraining;
use App\Models\TrainingAssignment;
use App\Models\UserLeaveRequest;
use App\Models\Employee_Personal_Details;
use App\Models\PromotionInfo;
use App\Models\TimeScheduler;
use App\Models\Employee;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;

use Exception;
use Facade\FlareClient\Time\Time;
use Illuminate\Support\Carbon;
use Illuminate\Support\Facades\Auth;
use SebastianBergmann\CodeCoverage\Report\Xml\Project;
use App\Models\Task;

use Illuminate\Support\Str;



use function GuzzleHttp\Promise\task;

class EmployeeController extends Controller
{
    public function Allemployeeshow($id)
    {
        $account_Info = Employee::findOrFail($id);
        return view('employee.allemployee_show', compact('account_Info'));
    }
    public function Allemployee100()
    {
        $allemployees = Employee::all();
        return view('employee.all_employee', compact('allemployees',));
    }
    public function employee()
    {
        $department = Department::all();
        return view('employee.members', compact('department'));
    }
    public function EmployeeStore(Request $request)
    {
        //dd($request->all());
        try {
            DB::beginTransaction();
            // account info  data insert start
            $cvName = "";
            if ($request->hasFile('upload_image') && $request->upload_image->isValid()) {
                $cvName = time() . '.' . $request->upload_image->extension();
                $request->upload_image->move(public_path('upload/employee_img'), $cvName);
            }
            $upload_cv = "";
            if ($request->hasFile('upload_cv') && $request->upload_cv->isValid()) {
                $upload_cv = time() . '.' . $request->upload_cv->extension();
                $request->upload_cv->move(public_path('upload/employee_cv'), $upload_cv);
            }

            // dd($upload_cv);
            // dd( $upload_cv);
            $account_info = Employee::create([
                'email'=>$request->email_address,
                'employee_name' => $request->employee_name,
                'department' => $request->department,
                'employee_id' => $request->employee_id,
                'username' => $request->username,
                'designation' => $request->designation,
                'upload_image' => $cvName,
                'upload_cv' => $upload_cv,
                'employee_status' => $request->employee_status,
                'password' => Hash::make($request->password),


            ]);

            // account info  data insert end

            // employee personal detailes data insert start
            Employee_Personal_Details::create([
                'account_id' => $account_info->id,
                'employee_name' => $request->employee_name1,
                'father_name' => $request->father_name,
                'mother_name' => $request->mother_name,
                'present_address' => $request->present_address,
                'permanent_address' => $request->permanent_address,
                'about_employee' => $request->about_employee,
                'date_of_birth' => $request->date_of_birth,
                'gender' => $request->gender,
                'martial_status' => $request->martial_status,
                'religion' => $request->religion,
                'mobile_number' => $request->mobile_number,
                'email_address' => $request->email_address,
                'emergency_contact' => $request->emergency_contact,
                'nid_number' => $request->nid_number,
                'birth_certificate_number' => $request->birth_certificate_number,
                'passport_number' => $request->passport_number,
                'joining_date' => $request->joining_date,
                'bank_account_number' => $request->bank_account_number,
                'reference_number_1' => $request->reference_number_1,
                'reference_number_2' => $request->reference_number_2,
                'relation_with_referal_1' => $request->relation_with_referal_1,
                'relation_with_referal_2' => $request->relation_with_referal_2,

            ]);
            // education and training start
            $upload_certificate = "";
            $training_upload_document = "";
            $upload_document = "";
            if ($request->hasFile('upload_certificate') && $request->upload_certificate->isValid()) {
                $upload_certificate = time() . '.' . $request->upload_certificate->extension();
                $request->upload_certificate->move(public_path('upload/education1'), $upload_certificate);
            }
            if ($request->hasFile('training_upload_document') && $request->training_upload_document->isValid()) {
                $training_upload_document = time() . '.' . $request->training_upload_document->extension();
                $request->training_upload_document->move(public_path('upload/education2'), $training_upload_document);

            }
            if ($request->hasFile('upload_document') && $request->upload_document->isValid()) {
                $upload_document = time() . '.' . $request->upload_document->extension();
                $request->upload_document->move(public_path('upload/education3'), $upload_document);
            }
            EducationAndTraining::create([
                'account_id' => $account_info->id,
                'degree_name' => $request->degree_name,
                'university' => $request->university,
                'passing_year' => $request->passing_year,
                'result' => $request->result,
                'upload_certificate' => $upload_certificate,
                'major_subject_name' => $request->major_subject_name,
                'achieved_reward' => $request->achieved_reward,
                'training_name' => $request->training_name,
                'training_institute_name' => $request->training_institute_name,
                'training_topic' => $request->training_topic,
                'training_duration' => $request->training_duration,
                'training_period' => $request->training_period,
                'training_location' => $request->training_location,
                'training_upload_document' => $training_upload_document,
                'company_name' => $request->company_name,
                'company_responsibility' => $request->company_responsibility,
                'experience_area' => $request->experience_area,
                'duration' => $request->duration,
                'from_date' => $request->from_date,
                'end_date' => $request->end_date,
                'company_location' => $request->company_location,
                'upload_document' => $upload_document,
            ]);
            // end education and training
            // start promosion isert data
            $approvals_doc_attachment = "";
            if ($request->hasFile('approval_doc_attachment') && $request->approval_doc_attachment->isValid()) {
                $approvals_doc_attachment = time() . '.' . $request->approval_doc_attachment->extension();
                $request->approval_doc_attachment->move(public_path('upload/promotion4'), $approvals_doc_attachment);
            }
            PromotionInfo::create([
                'account_id' => $account_info->id,
                'employee_name' => $request->employee_name2,
                'promotion_offer_issue_date' => $request->promotion_offer_issue_date,
                'effective_date' => $request->effective_date,
                'previous_designation' => $request->previous_designation,
                'promoted_designation' => $request->promoted_designation,
                'approval_doc_attachment' => $approvals_doc_attachment,
                'increment_amount' => $request->increment_amount,
                'previous_basic' => $request->previous_basic,
                'new_basic' => $request->new_basic,
                'previous_gross_salary' => $request->previous_gross_salary,
                'new_gross_salary' => $request->new_gross_salary,

            ]);
            // end promosion isert data
            // start training assignment
            $approval_docs_attachments = "";
            if ($request->hasFile('approval_doc_attachments') && $request->approval_doc_attachments->isValid()) {
                $approval_docs_attachments = time() . '.' . $request->approval_doc_attachments->extension();
                $request->approval_doc_attachments->move(public_path('upload/training5'), $approval_docs_attachments);
            }

            TrainingAssignment::create([
                'account_id' => $account_info->id,
                'training_program_name' => $request->training_program_name,
                'training_start_date' => $request->training_start_date,
                'training_end_date' => $request->training_end_date,
                'duration' => $request->duration,
                'location' => $request->location,
                'approval_doc_attachments' => $approval_docs_attachments,
                'invite_employee' => $request->invite_employee,
                'about_training' => $request->about_training,

            ]);
            $notification = array(
                'message' => 'project add sucessfully',
                'alert-type' => 'success'
            );
            DB::commit();
            return redirect()->back()->with($notification);
        } catch (\Exception $error) {

            dd($error);
            DB::rollBack();
        }

        // employee personal detailes data insert end
    }
    public function update(Request $request, $id)
    {
        $employee = Employee_Personal_Details::findOrFail($id);
        $employee->update([

            'employee_name' => $request->employee_name,
            'father_name' => $request->father_name,
            'mother_name' => $request->mother_name,
            'present_address' => $request->present_address,
            'permanent_address' => $request->permanent_address,
            'about_employee' => $request->about_employee,
            'date_of_birth' => $request->date_of_birth,
            'gender' => $request->gender,
            'martial_status' => $request->martial_status,
            'religion' => $request->religion,
            'mobile_number' => $request->mobile_number,
            'email_address' => $request->email_address,
            'emergency_contact' => $request->emergency_contact,
            'nid_number' => $request->nid_number,
            'birth_certificate_number' => $request->birth_certificate_number,
            'passport_number' => $request->passport_number,
            'joining_date' => $request->joining_date,
            'bank_account_number' => $request->bank_account_number,
            'reference_number_1' => $request->reference_number_1,
            'reference_number_2' => $request->reference_number_2,
            'relation_with_referal_1' => $request->relation_with_referal_1,
            'relation_with_referal_2' => $request->relation_with_referal_2,

        ]);

        return redirect()->back();
    }

     // =============employee my dashboard ===================================

    // =============employee assign project===================================
    public function assignProject(){
        $projects = Employee::join('assigned_to_tasks', 'employees.id', '=', 'assigned_to_tasks.employee_id')
                            ->join('tasks', 'assigned_to_tasks.task_id', '=', 'tasks.id')
                            ->join('add_projects', 'tasks.project_id', '=', 'add_projects.id')
                            ->where('employees.id',Auth::guard('employee')->user()->id)
                            ->distinct('employees.id')
                            ->select('add_projects.*','employees.id')
                            ->get();

        return view('employee.assign-project',compact('projects'));
    }

    //  ======================= task====================
     public function dashboard(){
         $total_process_task=Task::where('status',2)->count();
        //  dd($total_process_task);
         $total_quee_task=Task::where('status',1)->count();
         $total_done_task=Task::where('status',4)->count();
         $projects_Count = AddProject::count();
         $task_Count = Task::count();
         $task_Count_DeadLine = Task::where('end_date','<',Carbon::now())->count();
        //  dd($task_Count_DeadLine);

         //dd($tottal_process_task);
        return view('dashboard.employee',compact('total_process_task','total_done_task','total_quee_task','projects_Count','task_Count','task_Count_DeadLine'));


    }
   //  ======================= task====================

    // =============employee my profile ===================================
    public function myProfile($id){

        $myprofile = Employee::with(['personal_details','education_trainings','promotion_infos','training_assignments','department'])->where('id',$id)->first();
        // return $myprofile->personal_details->father_name;
        // dd($myprofile);
        return view('employee.my-profile',compact('myprofile'));


    }
    // =======================employee update start ============================
    public function employeeUpdate(Request $request,$id)
    {



        $accountInfo = Employee::findOrFail($id);


        $upload_image = $accountInfo->upload_image;
        if($request->hasFile('upload_image') && $request->upload_image->isValid()){
            $file_path = public_path('upload/employee_img/'.$accountInfo->upload_image);
            if(file_exists($file_path)){
                unlink($file_path);
            }
            $upload_image = time() . '.' . $request->upload_image->extension();
            $request->upload_image->move(public_path('upload/employee_img'), $upload_image);
        }

        $upload_cv = $accountInfo->upload_cv;
        if($request->hasFile('upload_cv') && $request->upload_cv->isValid()){
            $file_path = public_path('upload/employee_cv/'.$accountInfo->upload_cv);
            if(file_exists($file_path)){
                unlink($file_path);
            }
            $upload_cv = Str::random(10).time(). '.' . $request->upload_cv->extension();
            $request->upload_cv->move(public_path('upload/employee_cv'), $upload_cv);
        }

        $accountInfo->update([
        'employee_name' => $request->employee_name,
        'employee_id' => $request->employee_id,
        'designation' => $request->designation,
        'employee_status' => $request->employee_status,
        'username' => $request->username,
        // 'password' => $request->password,
        'password' => Hash::make($request->password),

        'upload_image' => $upload_image,
        'upload_cv' => $upload_cv,

        ]);
        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }


    // =======================employee update end ============================


    public function personalDetailsUpdate(Request $request, $id)
    {

        // $Employee_Personal_Details_id = $request->id;
        $employee = Employee_Personal_Details::findOrFail($id)->update([
        // $employee->update([

            'employee_name' => $request->employee_name,
            'father_name' => $request->father_name,
            'mother_name' => $request->mother_name,
            'present_address' => $request->present_address,
            'permanent_address' => $request->permanent_address,
            'about_employee' => $request->about_employee,
            'date_of_birth' => $request->date_of_birth,
            'gender' => $request->gender,
            'martial_status' => $request->martial_status,
            'religion' => $request->religion,
            'mobile_number' => $request->mobile_number,
            'email_address' => $request->email_address,
            'emergency_contact' => $request->emergency_contact,
            'nid_number' => $request->nid_number,
            'birth_certificate_number' => $request->birth_certificate_number,
            'passport_number' => $request->passport_number,
            'joining_date' => $request->joining_date,
            'bank_account_number' => $request->bank_account_number,
            'reference_number_1' => $request->reference_number_1,
            'reference_number_2' => $request->reference_number_2,
            'relation_with_referal_1' => $request->relation_with_referal_1,
            'relation_with_referal_2' => $request->relation_with_referal_2,

        ]);
        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

// ========================my profile education and training update start=================

public function educationUpdate(Request $request, $id){

    // dd($request->all());
    $educationAndTraning = EducationAndTraining::findOrFail($id);



    $upload_certificate = $educationAndTraning->upload_certificate;
    if($request->hasFile('upload_certificate') && $request->upload_certificate->isValid()){
        $file_path = public_path('upload/education1/'.$upload_certificate);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $upload_certificate = Str::random(12).time() . '.' . $request->upload_certificate->extension();
        $request->upload_certificate->move(public_path('upload/education1'), $upload_certificate);

    }

    $training_upload_document = $educationAndTraning->training_upload_document;
// dd($request->training_upload_document);
    if($request->hasFile('training_upload_document') && $request->training_upload_document->isValid()){
        $file_path = public_path('upload/education2/'.$training_upload_document);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $training_upload_document = Str::random(14).time() . '.' . $request->training_upload_document->extension();
        $request->training_upload_document->move(public_path('upload/education2'), $training_upload_document);
    }


    $upload_document = $educationAndTraning->upload_document;
    if($request->hasFile('upload_document') && $request->upload_document->isValid()){
        $file_path = public_path('upload/education3/'. $upload_document);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $upload_document = Str::random(16).time() . '.' . $request->upload_document->extension();
        $request->upload_document->move(public_path('upload/education3'), $upload_document);
    }

    $educationAndTraning->update([

        'degree_name' => $request->degree_name,
        'university' => $request->university,
        'passing_year' => $request->passing_year,
        'result' => $request->result,
        'upload_certificate' => $upload_certificate,
        'major_subject_name' => $request->major_subject_name,
        'achieved_reward' => $request->achieved_reward,
        'training_name' => $request->training_name,
        'training_institute_name' => $request->training_institute_name,
        'training_topic' => $request->training_topic,
        'training_duration' => $request->training_duration,
        'training_period' => $request->training_period,
        'training_location' => $request->training_location,
        'training_upload_document' => $training_upload_document,
        'company_name' => $request->company_name,
        'company_responsibility' => $request->company_responsibility,
        'experience_area' => $request->experience_area,
        'duration' => $request->duration,
        'from_date' => $request->from_date,
        'end_date' => $request->end_date,
        'company_location' => $request->company_location,
        'upload_document' => $upload_document,

    ]);

    $notification = array(
        'message' => 'Update sucessfully',
        'alert-type' => 'success'
    );
    return redirect()->back()->with($notification);
    


}

// ========================my profile education and training update end =================


// ========================my profile Promotion Infoupdate start=================

public function promotionInfoUpdate(Request $request, $id){

    //dd($request->all());
    $promotionInfo = PromotionInfo::findOrFail($id);


    $approval_doc_attachment = $promotionInfo->approval_doc_attachment;
    if($request->hasFile('approval_doc_attachment') && $request->approval_doc_attachment->isValid()){
        $file_path = public_path('upload/promotion4/'. $approval_doc_attachment);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $approval_doc_attachment = Str::random(18).time() . '.' . $request->approval_doc_attachment->extension();
        $request->approval_doc_attachment->move(public_path('upload/promotion4'), $approval_doc_attachment);
    }

    $promotionInfo->update([

        'employee_name' => $request->employee_name,
        'promotion_offer_issue_date' => $request->promotion_offer_issue_date,
        'effective_date' => $request->effective_date,
        'previous_designation' => $request->previous_designation,
        'promoted_designation' => $request->promoted_designation,
        'approval_doc_attachment' => $approval_doc_attachment,
        'increment_amount' => $request->increment_amount,
        'previous_basic' => $request->previous_basic,
        'new_basic' => $request->new_basic,
        'previous_gross_salary' => $request->previous_gross_salary,
        'new_gross_salary' => $request->new_gross_salary,
    ]);

    $notification = array(
        'message' => 'Update sucessfully',
        'alert-type' => 'success'
    );
    return redirect()->back()->with($notification);
}

// ========================my profile Promotion Info update end=================


// ========================my profile Promotion Info update end=================

public function trainingAssignmentUpdate(Request $request, $id){

    //dd($request);
    $trainingAssignment = TrainingAssignment::findOrFail($id);


    $approval_doc_attachments = $trainingAssignment->approval_doc_attachments;
    if($request->hasFile('approval_doc_attachments') && $request->approval_doc_attachments->isValid()){
        $file_path = public_path('upload/training5/'.$approval_doc_attachments);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $approval_doc_attachments= Str::random(10).time().'.'.$request->approval_doc_attachments->extension();
        //dd($approval_doc_attachments);
        $request->approval_doc_attachments->move(public_path('upload/training5'), $approval_doc_attachments);
    }

    $trainingAssignment->update([

        'training_program_name' => $request->training_program_name,
        'training_start_date' => $request->training_start_date,
        'training_end_date' => $request->training_end_date,
        'duration' => $request->duration,
        'location' => $request->location,
        'approval_doc_attachments' => $approval_doc_attachments,
        'invite_employee' => $request->invite_employee,
        'about_training' => $request->about_training,
    ]);
    $notification = array(
        'message' => 'Update sucessfully',
        'alert-type' => 'success'
    );
    return redirect()->back()->with($notification);
}
// ========================my profile Promotion Info update end=================


  // =============employee leave ====================================
    public function leave(){
    $user_id=$this->get_user_id();
    $employees=Employee::all();
    return view('employee.employee-leave',compact('employees','user_id'));
}
//================ employee leave ends================================

   // =============employee leave request====================================
   public function leave_request(Request $request){

    $this->validate($request,[
        'file' => 'required|mimes:png,jpg,jpeg,csv,txt,xlx,xls,pdf|max:2048',
     ]);
    $fileName = time().'.'.$request->file->extension();
    $request->file->move(public_path('files'), $fileName);
    UserLeaveRequest::create([
        'employee_id' => $request->employee_id,
        'designation' => $request->designation,
        'date_from' => $request->date_from,
        'time_from' => $request->time_from,
        'to_date' => $request->to_date,
        'to_time' => $request->to_time,
        'department' => $request->department,
        'reason' => $request->reason,
        'description' => $request->description,
        'file' => $fileName,
    ]);
    $notification = array(
        'message' => 'leave request added sucessfully',
        'alert-type' => 'success'
    );
    return redirect()->back()->with($notification);
}

//=============== employee leave request end ==================================
    //=============== employee leave request end ==================================
    //================ leave request list starts =====================================
    public function leave_request_list(){
        $leave_lists=UserLeaveRequest::all();
        return view('employee.employee-leave-request',compact('leave_lists'));
    }
    //================ leave request list end ========================================
    //===================leave request approval starts================================
    public function leave_request_approval($id){

        $approval=UserLeaveRequest::where('employee_id',$id)->first();
        $approval->approve=1;
        $approval->save();
        $notification = array(
            'message' => 'Request has been approved',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }
    //================== leave request approval ends==================================
    //================== leave request destroy starts=================================
    public function leave_request_destroy($id){
        $approval=UserLeaveRequest::where('employee_id',$id)->first();
        $approval->delete();
        $notification = array(
            'message' => 'The request is deleted',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }
    //================= leave request destroy ends=====================================
    //================ get user id starts ==============================================
    public function get_user_id(){
        $user=Auth::guard('employee')->user()->employee_id;
        return $user;
    }
    //===================get user id ends==================================



    // =============employee ticket project===================================
    public function ticket(){
        return view('employee.employee-tikets');
    }
    // =============employee attendance ====================================
    public function attendance(){
        return view('employee.employee-attendance');
    }


    // =============employee leave ====================================

    // =============employee calender ====================================
    public function calender(){
        return view('employee.calender');
    }

    // =============employee task list ====================================
    public function tasklist($project){
        $tasks = Task::join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
                ->join('employees', 'assigned_to_tasks.employee_id', '=', 'employees.id')
                ->where('employees.id',Auth::guard('employee')->user()->id)
                ->where('project_id',$project)
                ->get();
        // dd($tasks);

        $todo = Task::where('project_id',$project)->where('status',1)->get();
        $inProgress = Task::where('project_id',$project)->where('status',2)->get();
        $testing = Task::where('project_id',$project)->where('status',3)->get();
        $done = Task::where('project_id',$project)->where('status',4)->get();

        return view('employee.task-list',compact('tasks','todo','inProgress','testing','done'));
    }

    public function getSingleTaskDetails($id)
    {
        $task = Task::find($id);

        return $task;
    }


    public function setTime(Request $request){
        $user=Auth::guard('employee')->user();
        $user_id=$user->id;
        $check_timer=TimeScheduler::where('task_id',$request->task_id)->where('status',0)->where('user_id',$user_id)->get();
        if(count($check_timer)==0){
            TimeScheduler::create([
                'user_id'=> $user_id,
                'start_time' => Carbon::now()->format('h:i:s'),
                'task_id' => $request->task_id,
            ]);

        }else{
           dd("You are already runninng a task.please Quit");
        }

    }
    public function endTime(Request $request){
        $user=Auth::guard('employee')->user();
        $user_id=$user->id;
        $timer=TimeScheduler::where('task_id',$request->task_id)->where('status',0)->where('user_id',$user_id)->latest()->first();
        // if(!$timer){
        //     TimeScheduler::create([
        //        'status' =>1,
        //     ]);
        // }
        $timer->end_time = Carbon::now()->format('h:i:s');
        $start_time = strtotime($timer->start_time);
        $end_time = strtotime(Carbon::now()->format('h:i:s'));

        $timer->duration = $end_time-$start_time;
        $timer->status = 1;
        $timer->update();
    }


    public function getTime($taskid)
    {
        try{
            $user=Auth::guard('employee')->user();
            $user_id=$user->id;
            $getTime = TimeScheduler::where('task_id',$taskid)->where('status',0)->where('user_id',$user_id)->latest()->first();
            if(!$getTime){
               return response()->json(['error'=>"not Found"],404);
            }
            return response()->json($getTime);
        }catch(Exception $e){
            return response()->json(['error'=>"not Found"],404);
        }
    }
}
<<<==============================employe=====================controller============================add========end========>>>

<!-- db begintransaction for insert multiple data start -->
DB::beginTransaction();

try {
    DB::insert(...);
    DB::insert(...);
    DB::insert(...);

    DB::commit();
    // all good
} catch (\Exception $e) {
    DB::rollback();
    // something went wrong
}
<!-- db begintransaction for insert multiple data start end  -->

<!-- =====================for serching code start ================ -->


    public function showInvoice(Request $request){

        if($request->keyword != ''){
        $invoices = Invoice::where('invoice_id','LIKE','%'.$request->keyword.'%')
        ->orWhere('invoice_id','LIKE','%'.$request->keyword.'%')
        ->orWhere('payment_received','LIKE','%'.$request->keyword.'%')
        ->orWhere('payment_due','LIKE','%'.$request->keyword.'%')
        ->orWhere('billing_date','LIKE','%'.$request->keyword.'%')
        ->orWhere('due_date','LIKE','%'.$request->keyword.'%')
        ->orWhere('note','LIKE','%'.$request->keyword.'%')
        ->orWhere('status','LIKE','%'.$request->keyword.'%')
        ->get();
        }
        return response()->json([
           'invoices' => $invoices
        ]);


    }

<!--================================for searching code end=======================  -->

<!-- ====================================employee ==================================== -->

<?php
namespace App\Http\Controllers\Employee;
use App\Http\Controllers\Controller;
use App\Models\Account_Info;
use App\Models\AddProject;
use App\Models\AssignedToTask;
use App\Models\Attendance;
use App\Models\Department;
use Illuminate\Http\Request;
use App\Models\EducationAndTraining;
use App\Models\TrainingAssignment;
use App\Models\UserLeaveRequest;
use App\Models\Employee_Personal_Details;
use App\Models\PromotionInfo;
use App\Models\TimeScheduler;
use App\Models\Employee;
use App\Models\Holiday;
use Illuminate\Support\Facades\DB;
// use Illuminate\Support\Facades\Hash;

use Exception;
use Facade\FlareClient\Time\Time;
use Carbon\Carbon;
use Illuminate\Support\Facades\Auth;
use SebastianBergmann\CodeCoverage\Report\Xml\Project;
use App\Models\Task;

use Illuminate\Support\Str;
use PhpParser\Node\Expr\Empty_;

// use function GuzzleHttp\Promise\task;

class EmployeeController extends Controller
{
    public function Allemployeeshow( $role , $id)
    {


        $myprofile = Employee::with(['personal_details','education_trainings','promotion_infos','training_assignments','department'])->where('id',$id)->first();
        return view('employee.allemployee_show',compact('myprofile'));

    }

    // =============employee delete code for admin start here==============================
    public function employeeDelete($role, $id)
    {


        $myprofile = Employee::with(['personal_details','education_trainings','promotion_infos','training_assignments','department'])->find($id);



       if($myprofile->education_trainings == '')
        {


            $employeeImage = $myprofile->upload_image;
            $file_path = public_path('upload/employee_img/'. $employeeImage);
            if(file_exists($file_path) && $myprofile->upload_image){
                unlink($file_path);
            }


            $employeeCv = $myprofile->upload_cv;
            $file_path = public_path('upload/employee_cv/'. $employeeCv);
            if(file_exists($file_path) && $myprofile->upload_cv){
                unlink($file_path);
            }
            $promotionInfo = optional($myprofile->promotion_infos)->approval_doc_attachment;
            $file_path = public_path('upload/promotion4/'. $promotionInfo);
            if(file_exists($file_path) && optional($myprofile->promotion_infos)->approval_doc_attachment){
                unlink($file_path);
            }

            $trainingAssign = optional($myprofile->training_assignments)->approval_doc_attachments;
            $file_path = public_path('upload/training5/'. $trainingAssign);
            if(file_exists($file_path) && optional($myprofile->training_assignments)->approval_doc_attachments ){
                unlink($file_path);
            }


            try{

                $myprofile->delete();
                $personal_details = Employee_Personal_Details::where('account_id',$id)->delete();
                $promotion_infos = PromotionInfo::where('account_id',$id)->delete();
                $training_assignments = TrainingAssignment::where('account_id',$id)->delete();




            } catch(\Exception $e){
                dd($e);
            }
            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);
        }




        elseif($myprofile->promotion_infos == '')
        {
            $myprofile = Employee::find($id);
            $employeeImage = $myprofile->upload_image;
            $file_path = public_path('upload/employee_img/'. $employeeImage);
            if(file_exists($file_path) && $myprofile->upload_image){
                unlink($file_path);
            }


            $employeeCv = $myprofile->upload_cv;
            $file_path = public_path('upload/employee_cv/'. $employeeCv);
            if(file_exists($file_path) && $myprofile->upload_cv){
                unlink($file_path);
            }

            $educationDoc1 = optional($myprofile->education_trainings)->upload_certificate;
            $file_path = public_path('upload/education1/'. $educationDoc1);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->upload_certificate ){
                unlink($file_path);
            }

            $educationDoc2 = optional($myprofile->education_trainings)->training_upload_document;
            $file_path = public_path('upload/education2/'. $educationDoc2);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->training_upload_document){
                unlink($file_path);
            }

            $educationDoc3 = optional($myprofile->education_trainings)->upload_document;
            $file_path = public_path('upload/education3/'. $educationDoc3);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->upload_document){
                unlink($file_path);
            }


            $trainingAssign =optional( $myprofile->training_assignments)->approval_doc_attachments;
            $file_path = public_path('upload/training5/'. $trainingAssign);
            if(file_exists($file_path) && optional( $myprofile->training_assignments)->approval_doc_attachments){
                unlink($file_path);
            }


            try{
                $myprofile->delete();
                $education_trainings = EducationAndTraining::where('account_id',$id)->delete();
                $personal_details = Employee_Personal_Details::where('account_id',$id)->delete();
                $training_assignments = TrainingAssignment::where('account_id',$id)->delete();
            } catch(\Exception $e){

            dd($e);


            }
            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);
        }





        elseif($myprofile->training_assignments == '')
        {
            $myprofile = Employee::find($id);
            $employeeImage = $myprofile->upload_image;
            $file_path = public_path('upload/employee_img/'. $employeeImage);
            if(file_exists($file_path)){
                unlink($file_path);
            }


            $employeeCv = $myprofile->upload_cv;
            $file_path = public_path('upload/employee_cv/'. $employeeCv);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc1 = $myprofile->education_trainings->upload_certificate;
            $file_path = public_path('upload/education1/'. $educationDoc1);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc2 = $myprofile->education_trainings->training_upload_document;
            $file_path = public_path('upload/education2/'. $educationDoc2);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc3 = $myprofile->education_trainings->upload_document;
            $file_path = public_path('upload/education3/'. $educationDoc3);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $promotionInfo = $myprofile->promotion_infos->approval_doc_attachment;
            $file_path = public_path('upload/promotion4/'. $promotionInfo);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            try{
               $myprofile->delete();
               $education_trainings = EducationAndTraining::where('account_id',$id)->delete();
               $promotion_infos = PromotionInfo::where('account_id',$id)->delete();
               $personal_details = Employee_Personal_Details::where('account_id',$id)->delete();
            } catch(\Exception $e){
                dd($e);
            }
            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);
        }


        elseif($myprofile->employee__personal__details == '')
        {
            $myprofile = Employee::find($id);
            $employeeImage = $myprofile->upload_image;
            $file_path = public_path('upload/employee_img/'. $employeeImage);
            if(file_exists($file_path)){
                unlink($file_path);
            }
            $employeeCv = $myprofile->upload_cv;
            $file_path = public_path('upload/employee_cv/'. $employeeCv);
            if(file_exists($file_path) && $myprofile->upload_cv){
                unlink($file_path);
            }

            $educationDoc1 = optional($myprofile->education_trainings)->upload_certificate;
            $file_path = public_path('upload/education1/'. $educationDoc1);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->upload_certificate){
                unlink($file_path);
            }

            $educationDoc2 = optional($myprofile->education_trainings)->training_upload_document;
            $file_path = public_path('upload/education2/'. $educationDoc2);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->training_upload_document){
                unlink($file_path);
            }

            $educationDoc3 = optional($myprofile->education_trainings)->upload_document;
            $file_path = public_path('upload/education3/'. $educationDoc3);
            if(file_exists($file_path) && optional($myprofile->education_trainings)->upload_document){
                unlink($file_path);
            }

            $promotionInfo = optional($myprofile->promotion_infos)->approval_doc_attachment;
            $file_path = public_path('upload/promotion4/'. $promotionInfo);
            if(file_exists($file_path) && optional($myprofile->promotion_infos)->approval_doc_attachment){
                unlink($file_path);
            }



            try{

                $myprofile->delete();
                $education_trainings = EducationAndTraining::where('account_id',$id)->delete();
                $promotion_infos = PromotionInfo::where('account_id',$id)->delete();
                $training_assignments = TrainingAssignment::where('account_id',$id)->delete();

            } catch(\Exception $e){

            dd($e);


            }
            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);
        }

        else

        {
            $myprofile = Employee::find($id);
            $employeeImage = $myprofile->upload_image;
            $file_path = public_path('upload/employee_img/'. $employeeImage);
            if(file_exists($file_path)){
                unlink($file_path);
            }


            $employeeCv = $myprofile->upload_cv;
            $file_path = public_path('upload/employee_cv/'. $employeeCv);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc1 = $myprofile->education_trainings->upload_certificate;
            $file_path = public_path('upload/education1/'. $educationDoc1);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc2 = $myprofile->education_trainings->training_upload_document;
            $file_path = public_path('upload/education2/'. $educationDoc2);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $educationDoc3 = $myprofile->education_trainings->upload_document;
            $file_path = public_path('upload/education3/'. $educationDoc3);
            if(file_exists($file_path)){
                unlink($file_path);
            }



            $promotionInfo = $myprofile->promotion_infos->approval_doc_attachment;
            $file_path = public_path('upload/promotion4/'. $promotionInfo);
            if(file_exists($file_path)){
                unlink($file_path);
            }

            $trainingAssign = $myprofile->training_assignments->approval_doc_attachments;
            $file_path = public_path('upload/training5/'. $trainingAssign);
            if(file_exists($file_path)){
                unlink($file_path);
            }


            try{
                $myprofile->delete();
                $education_trainings = EducationAndTraining::where('account_id',$id)->delete();
                $personal_details = Employee_Personal_Details::where('account_id',$id)->delete();
                $promotion_infos = PromotionInfo::where('account_id',$id)->delete();
                $training_assignments = TrainingAssignment::where('account_id',$id)->delete();
            } catch(\Exception $e){

            dd($e);
            }
            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);

        }


    }



    // =============employee delete code for admin end here==============================



    // ===============employee personal details from admin start here====================

    public function personalDetailsDelete($role,$id)
    {
        $personalDetailsDelete = Employee_Personal_Details::findorFail($id);
        $personalDetailsDelete->delete();

        $notification = array(
            'message' => 'Deleted sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    // ===============employee personal details from admin end here====================



    // ===============employee personal details from admin start here====================

    public function educationTrainingDelete($role,$id)
    {
        $educationTrainingDelete = EducationAndTraining::findorFail($id);

        $educationDoc1 = $educationTrainingDelete->upload_certificate;
        $file_path = public_path('upload/education1/'. $educationDoc1);
        if(file_exists($file_path)){
            unlink($file_path);
        }

        $educationDoc2 = $educationTrainingDelete->training_upload_document;
        $file_path = public_path('upload/education2/'. $educationDoc2);
        if(file_exists($file_path)){
            unlink($file_path);
        }

        $educationDoc3 = $educationTrainingDelete->upload_document;
        $file_path = public_path('upload/education3/'. $educationDoc3);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $educationTrainingDelete->delete();

        $notification = array(
            'message' => 'Deleted sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    // ===============employee personal details from admin end here====================


    // ===============employee promotionInfoDelete details from admin start here====================

    public function promotionInfoDelete($role,$id)
    {
        $promotionInfoDelete = PromotionInfo::findorFail($id);



        $promotionInfo = $promotionInfoDelete->approval_doc_attachment;
        $file_path = public_path('upload/promotion4/'. $promotionInfo);
        if(file_exists($file_path)){
            unlink($file_path);
        }

        $promotionInfoDelete->delete();

        $notification = array(
            'message' => 'Deleted sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    // ===============employee promotionInfoDelete details from admin end here====================


    // ===============employee trainingInfoDelete details from admin start here====================

    public function trainingInfoDelete($role,$id)

    {
        $trainingInfoDelete = TrainingAssignment::findorFail($id);

        $trainingAssign = $trainingInfoDelete->approval_doc_attachments;
        $file_path = public_path('upload/training5/'. $trainingAssign);
        if(file_exists($file_path)){
            unlink($file_path);
        }
        $trainingInfoDelete->delete();

        $notification = array(
            'message' => 'Deleted sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    // ===============employee trainingInfoDelete details from admin end here====================





    public function Allemployee100()
    {
        $allemployees = Employee::with('departmentName','personal_details')->get();

        return view('employee.all_employee', compact('allemployees',));
    }
    public function employee()
    {
        $department = Department::all();
        return view('employee.members', compact('department'));
    }
    public function EmployeeStore(Request $request)
    {
        $rules =[
            'upload_image' => 'required|mimes:png,jpg, jpeg,image|max:2048',
            'upload_cv' => 'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
            'employee_id'=>'required',
            'employee_name'=>'required',
            'department'=>'required',
            'email'=>'required',
            'username'=>'required|unique:employees',
            'employee_status'=>'required',
            'password'=>'required',
            // ==================employeess table validation end =============================
            // ===============employees personal details table validation start ==============================
            'father_name'=>'required',
            'mother_name'=>'required',
            'permanent_address'=>'required',
            'date_of_birth'=>'required',
            'gender'=>'required',
            'martial_status'=>'required',
            'religion'=>'required',
            'nid_number'=>'required|max:17|min:10',
            'reference_info_1'=>'required',
            'relation_with_referal_1'=>'required',
            'reference_number_1'=>'required|max:11|min:11',
             // ===============employees personal details table validation end ==============================
            // ===============employees education and training  table validation start ==============================
            'degree_name'=>'required',
            'university'=>'required',
            'passing_year'=>'required',
            'result'=>['required','regex:/^-?(?:\d+|\d*\.\d{1,2})$/'],
            'upload_certificate'=>'required|mimes:png,jpg,doc,docx,jpeg,pdf|max:2048image|max:2048',
            'training_upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
            'upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
            // ===============employees education and training  table validation start ==============================
            // ===============employees promotion info table validation start ==============================
            'approval_doc_attachment'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


            'approval_doc_attachments'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


        ];
        $message =  [
            'upload_image.required'=>'you have to input png, jpg, jpeg format and size maximum 2 MB',
            'email.required'=>'unique email required',
            'username.required'=>'unique user name required',

            'result.required'=>'You have to input floating value',
            'result.regex'=>'input value must be numeric or floating ',

        ];



    if($request->birth_certificate_number){
        $rules['birth_certificate_number'] =['max:17|min:10'];

    }
    if($request->emergency_contact){
        $rules['emergency_contact'] =['max:11|min:11'];

    }
    if($request->mobile_number){
        $rules['mobile_number'] =['max:11|min:11'];

    }
    if($request->reference_number_2){
        $rules['reference_number_2'] =['max:11|min:11'];

    }
    if($request->increment_amount){
        $rules['increment_amount'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
        $message['increment_amount.regex']='input value must be numeric or floating';

    }
    if($request->new_basic){
        $rules['new_basic'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
        $message['new_basic.regex']='input value must be numeric or floating';

    }
    if($request->previous_gross_salary){
        $rules['previous_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
        $message['previous_gross_salary.regex']='input value must be numeric or floating';

    }
    if($request->new_gross_salary){
        $rules['new_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
        $message['new_gross_salary.regex']='input value must be numeric or floating';

    }

    $validateData = $request->validate( $rules, $message );

        try {
            DB::beginTransaction();
            // account info  data insert start
            $cvName = "";
            if ($request->hasFile('upload_image') && $request->upload_image->isValid()) {
                $cvName = time() . '.' . $request->upload_image->extension();
                $request->upload_image->move(public_path('upload/employee_img'), $cvName);
            }
            $upload_cv = "";
            if ($request->hasFile('upload_cv') && $request->upload_cv->isValid()) {
                $upload_cv = time() . '.' . $request->upload_cv->extension();
                $request->upload_cv->move(public_path('upload/employee_cv'), $upload_cv);
            }

            $account_info = Employee::create([
                'email'         => $request->email,
                'employee_name' => $request->employee_name,
                'department'    => $request->department,
                'employee_id'   => $request->employee_id,
                'username'      => $request->username,
                'designation'   => $request->designation,
                'upload_image'  => $cvName,
                'upload_cv'     => $upload_cv,
                'employee_status' => $request->employee_status,
                'password'        => bcrypt($request->password),
            ]);


            // employee personal detailes data insert start
            Employee_Personal_Details::create([
                'account_id' => $account_info->id,
                'father_name'       => $request->father_name,
                'mother_name'       => $request->mother_name,
                'present_address'   => $request->present_address,
                'permanent_address' => $request->permanent_address,
                'about_employee' => $request->about_employee,
                'date_of_birth' => $request->date_of_birth,
                'gender' => $request->gender,
                'martial_status' => $request->martial_status,
                'religion' => $request->religion,
                'mobile_number' => $request->mobile_number,
                'emergency_contact' => $request->emergency_contact,
                'nid_number' => $request->nid_number,
                'birth_certificate_number' => $request->birth_certificate_number,
                'passport_number' => $request->passport_number,
                'joining_date' => $request->joining_date,
                'bank_account_number' => $request->bank_account_number,
                'reference_info_1' => $request->reference_info_1,
                'reference_info_2' => $request->reference_info_2,
                'reference_number_1' => $request->reference_number_1,
                'reference_number_2' => $request->reference_number_2,
                'relation_with_referal_1' => $request->relation_with_referal_1,
                'relation_with_referal_2' => $request->relation_with_referal_2,

            ]);
            // education and training start
            $upload_certificate = "";
            $training_upload_document = "";
            $upload_document = "";
            if ($request->hasFile('upload_certificate') && $request->upload_certificate->isValid()) {
                $upload_certificate = time() . '.' . $request->upload_certificate->extension();
                $request->upload_certificate->move(public_path('upload/education1'), $upload_certificate);
            }
            if ($request->hasFile('training_upload_document') && $request->training_upload_document->isValid()) {
                $training_upload_document = time() . '.' . $request->training_upload_document->extension();
                $request->training_upload_document->move(public_path('upload/education2'), $training_upload_document);

            }
            if ($request->hasFile('upload_document') && $request->upload_document->isValid()) {
                $upload_document = time() . '.' . $request->upload_document->extension();
                $request->upload_document->move(public_path('upload/education3'), $upload_document);
            }
            EducationAndTraining::create([
                'account_id' => $account_info->id,
                'degree_name' => $request->degree_name,
                'university' => $request->university,
                'passing_year' => $request->passing_year,
                'result' => $request->result,
                'upload_certificate' => $upload_certificate,
                'major_subject_name' => $request->major_subject_name,
                'achieved_reward' => $request->achieved_reward,
                'training_name' => $request->training_name,
                'training_institute_name' => $request->training_institute_name,
                'training_topic' => $request->training_topic,
                'training_duration' => $request->training_duration,
                'training_period' => $request->training_period,
                'training_location' => $request->training_location,
                'training_upload_document' => $training_upload_document,
                'company_name' => $request->company_name,
                'company_responsibility' => $request->company_responsibility,
                'experience_area' => $request->experience_area,
                'duration' => $request->duration,
                'from_date' => $request->from_date,
                'end_date' => $request->end_date,
                'company_location' => $request->company_location,
                'upload_document' => $upload_document,
            ]);
            // end education and training

            // start promosion isert data
            $approvals_doc_attachment = "";
            if ($request->hasFile('approval_doc_attachment') && $request->approval_doc_attachment->isValid()) {
                $approvals_doc_attachment = time() . '.' . $request->approval_doc_attachment->extension();
                $request->approval_doc_attachment->move(public_path('upload/promotion4'), $approvals_doc_attachment);
            }
            PromotionInfo::create([
                'account_id'                 => $account_info->id,
                'promotion_offer_issue_date' => $request->promotion_offer_issue_date,
                'effective_date'             => $request->effective_date,
                'previous_designation'       => $request->previous_designation,
                'promoted_designation'       => $request->promoted_designation,
                'approval_doc_attachment'    => $approvals_doc_attachment,
                'increment_amount'           => $request->increment_amount,
                'previous_basic'             => $request->previous_basic,
                'new_basic'                  => $request->new_basic,
                'previous_gross_salary'      => $request->previous_gross_salary,
                'new_gross_salary'           => $request->new_gross_salary,

            ]);
            // end promosion isert data
            // start training assignment
            $approval_docs_attachments = "";
            if ($request->hasFile('approval_doc_attachments') && $request->approval_doc_attachments->isValid()) {
                $approval_docs_attachments = time() . '.' . $request->approval_doc_attachments->extension();
                $request->approval_doc_attachments->move(public_path('upload/training5'), $approval_docs_attachments);
            }

            TrainingAssignment::create([
                'account_id' => $account_info->id,
                'training_program_name' => $request->training_program_name,
                'training_start_date' => $request->training_start_date,
                'training_end_date' => $request->training_end_date,
                'duration' => $request->duration,
                'location' => $request->location,
                'approval_doc_attachments' => $approval_docs_attachments,
                'about_training' => $request->about_training,

            ]);
            $notification = array(
                'message' => 'Employee add sucessfully',
                'alert-type' => 'success'
            );
            DB::commit();
            return redirect()->back()->with($notification);
        } catch (\Exception $error) {
            DB::rollBack();
            $notification = array(
                'message' => 'Some Error Occured Please Try Again!',
                'alert-type' => 'error '
            );
            return redirect()->back()->with($notification);
        }

        // employee personal detailes data insert end
    }


    // =============employee assign project===================================
    public function assignProject(){
        // $projects = Employee::join('assigned_to_tasks', 'employees.id', '=', 'assigned_to_tasks.employee_id')
        //                     ->join('tasks', 'assigned_to_tasks.task_id', '=', 'tasks.id')
        //                     ->join('add_projects', 'tasks.project_id', '=', 'add_projects.id')
        //                     ->where('employees.id',Auth::guard('employee')->user()->id)
        //                     ->distinct('add_projects.id')
        //                     ->select('add_projects.*','employees.id')
        //                     ->get();

        $projects = AddProject::join('tasks','add_projects.id','=','tasks.project_id')
                            ->join('assigned_to_tasks','tasks.id', '=' ,'assigned_to_tasks.task_id')
                            ->join('employees','assigned_to_tasks.employee_id','=','employees.id')
                            ->where('employees.id',Auth::guard('employee')->user()->id)
                            ->distinct('add_projects.id')
                            ->get('add_projects.*');



        return view('employee.assign-project',compact('projects'));
    }

    public function assignedEmployee($id)
    {
        $employee = Employee::join('assign_to_projects','employees.id','=','assign_to_projects.employee_id')
                            ->where('assign_to_projects.project_id','=',$id)
                            // ->join('add_projects','assign_to_projects.project_id','=','add_projects.id')
                            ->get('employees.*');

        return response()->json($employee);
    }

    //  ======================= task====================
     public function dashboard(){
        $total_process_task=Task::where('status',2)->count();
        $total_quee_task=Task::where('status',1)->count();
        $total_done_task=Task::where('status',4)->count();
        $projects_Count = AddProject::count();
        $task_Count = Task::count();
        $task_Count_DeadLine = Task::where('end_date','<',Carbon::now())->count();
        //dd($tottal_process_task);
        return view('dashboard.employee',compact('total_process_task','total_done_task','total_quee_task','projects_Count','task_Count','task_Count_DeadLine'));


    }
   //  ======================= task====================

    // =============employee my profile ===================================
    public function myProfile($id){
        $myprofile = Employee::with(['personal_details','education_trainings','promotion_infos','training_assignments','department'])->where('id',$id)->first();
        return view('employee.my-profile',compact('myprofile'));
    }

    // =======================employee update start ============================
    public function employeeUpdate(Request $request,$id)
    {

        $rules =[
            'upload_image' => 'mimes:png,jpg, jpeg,image|max:2048',

            'upload_cv' => 'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',

            'employee_id'=>'required',
            'employee_name'=>'required',
            'employee_status'=>'required',
            'password'=>'required',
            // ==================employeess table validation end =============================
        ];
        $message =  [



        ];
      $validateData = $request->validate( $rules, $message );

        $accountInfo  = Employee::findOrFail($id);
        $upload_image = $accountInfo->upload_image;
        if($request->hasFile('upload_image') && $request->upload_image->isValid()){
            $file_path = public_path('upload/employee_img/'.$accountInfo->upload_image);
            if(!empty($upload_image) && file_exists($file_path)){
                unlink($file_path);
            }
            $upload_image = time() . '.' . $request->upload_image->extension();
            $request->upload_image->move(public_path('upload/employee_img'), $upload_image);
        }

        $upload_cv = $accountInfo->upload_cv;
        if($request->hasFile('upload_cv') && $request->upload_cv->isValid()){
            $file_path = public_path('upload/employee_cv/'.$accountInfo->upload_cv);
            if( !empty($upload_cv) && file_exists($file_path)){
                unlink($file_path);
            }
            $upload_cv = Str::random(10).time(). '.' . $request->upload_cv->extension();
            $request->upload_cv->move(public_path('upload/employee_cv'), $upload_cv);
        }

        $accountInfo->update([
            'employee_name' => $request->employee_name,
            'employee_id' => $request->employee_id,
            'designation' => $request->designation,
            'employee_status' => $request->employee_status,
            'username' => $request->username,
            'password' => bcrypt($request->password),
            'upload_image' => $upload_image,
            'upload_cv' => $upload_cv,
        ]);
        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }


    // =======================employee update end ============================


    public function personalDetailsUpdate(Request $request, $id)
    {
        $rules =[
          // ===============employees personal details table validation start ==============================
          'father_name'=>'required',
          'mother_name'=>'required',
          'permanent_address'=>'required',
          'date_of_birth'=>'required',
          'gender'=>'required',
          'martial_status'=>'required',
          'religion'=>'required',
          'nid_number'=>'required|max:17|min:10',
          'reference_info_1'=>'required',
          'relation_with_referal_1'=>'required',
          'reference_number_1'=>'required|max:11|min:11',
           // ===============employees personal details table validation end ==============================
        ];
        $message =  [


        ];
        if($request->birth_certificate_number){
            $rules['birth_certificate_number'] =['max:17|min:10'];

        }
        if($request->emergency_contact){
            $rules['emergency_contact'] =['max:11|min:11'];

        }
        if($request->mobile_number){
            $rules['mobile_number'] =['max:11|min:11'];

        }
        if($request->reference_number_2){
            $rules['reference_number_2'] =['max:11|min:11'];

        }

       $validateData = $request->validate( $rules, $message );

        // $Employee_Personal_Details_id = $request->id;
        $employee = Employee_Personal_Details::findOrFail($id)->update([
            'father_name' => $request->father_name,
            'mother_name' => $request->mother_name,
            'present_address' => $request->present_address,
            'permanent_address' => $request->permanent_address,
            'about_employee' => $request->about_employee,
            'date_of_birth' => $request->date_of_birth,
            'gender' => $request->gender,
            'martial_status' => $request->martial_status,
            'religion' => $request->religion,
            'mobile_number' => $request->mobile_number,
            'emergency_contact' => $request->emergency_contact,
            'nid_number' => $request->nid_number,
            'birth_certificate_number' => $request->birth_certificate_number,
            'passport_number' => $request->passport_number,
            'joining_date' => $request->joining_date,
            'bank_account_number' => $request->bank_account_number,
            'reference_info_1' => $request->reference_info_1,
             'reference_info_2' => $request->reference_info_2,
            'reference_number_1' => $request->reference_number_1,
            'reference_number_2' => $request->reference_number_2,
            'relation_with_referal_1' => $request->relation_with_referal_1,
            'relation_with_referal_2' => $request->relation_with_referal_2,

        ]);
        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }



    public function educationUpdate(Request $request, $id){
        $rules =[
              // ===============employees education and training  table validation start ==============================
              'degree_name'=>'required',
              'university'=>'required',
              'passing_year'=>'required',
              'result'=>['required','regex:/^-?(?:\d+|\d*\.\d{1,2})$/'],
              'upload_certificate'=>'required|mimes:png,jpg,doc,docx,jpeg,pdf|max:2048image|max:2048',
              'training_upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
              'upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
              // ===============employees education and training  table validation start ==============================
            ];
            $message =  [


                'result.required'=>'You have to input floating value',
                'result.regex'=>'input value must be numeric or floating ',

            ];
       $validateData = $request->validate( $rules, $message );

            // dd($request->all());
            $educationAndTraning = EducationAndTraining::findOrFail($id);
            $upload_certificate = $educationAndTraning->upload_certificate;
            if($request->hasFile('upload_certificate') && $request->upload_certificate->isValid()){
                $file_path = public_path('upload/education1/'.$upload_certificate);
                if( !empty($upload_certificate) &&  file_exists($file_path)){
                    unlink($file_path);
                }
                $upload_certificate = Str::random(12).time() . '.' . $request->upload_certificate->extension();
                $request->upload_certificate->move(public_path('upload/education1'), $upload_certificate);

            }
            $training_upload_document = $educationAndTraning->training_upload_document;

            if($request->hasFile('training_upload_document') && $request->training_upload_document->isValid()){
                $file_path = public_path('upload/education2/'.$training_upload_document);
                if(!empty($training_upload_document) &&  file_exists($file_path)){
                    unlink($file_path);
                }
                $training_upload_document = Str::random(14).time() . '.' . $request->training_upload_document->extension();
                $request->training_upload_document->move(public_path('upload/education2'), $training_upload_document);
            }


            $upload_document = $educationAndTraning->upload_document;
            if($request->hasFile('upload_document') && $request->upload_document->isValid()){
                $file_path = public_path('upload/education3/'. $upload_document);
                if(!empty($upload_document) && file_exists($file_path)){
                    unlink($file_path);
                }
                $upload_document = Str::random(16).time() . '.' . $request->upload_document->extension();
                $request->upload_document->move(public_path('upload/education3'), $upload_document);
            }

            $educationAndTraning->update([

                'degree_name' => $request->degree_name,
                'university' => $request->university,
                'passing_year' => $request->passing_year,
                'result' => $request->result,
                'upload_certificate' => $upload_certificate,
                'major_subject_name' => $request->major_subject_name,
                'achieved_reward' => $request->achieved_reward,
                'training_name' => $request->training_name,
                'training_institute_name' => $request->training_institute_name,
                'training_topic' => $request->training_topic,
                'training_duration' => $request->training_duration,
                'training_period' => $request->training_period,
                'training_location' => $request->training_location,
                'training_upload_document' => $training_upload_document,
                'company_name' => $request->company_name,
                'company_responsibility' => $request->company_responsibility,
                'experience_area' => $request->experience_area,
                'duration' => $request->duration,
                'from_date' => $request->from_date,
                'end_date' => $request->end_date,
                'company_location' => $request->company_location,
                'upload_document' => $upload_document,

            ]);



            $notification = array(
                'message' => 'Update sucessfully',
                'alert-type' => 'success'
            );
            return redirect()->back()->with($notification);

    }




    public function promotionInfoUpdate(Request $request, $id){
        $rules=[
            'approval_doc_attachment'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


         ];
         $message = [

            ];
            if($request->increment_amount){
                $rules['increment_amount'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['increment_amount.regex']='input value must be numeric or floating';

            }
            if($request->new_basic){
                $rules['new_basic'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['new_basic.regex']='input value must be numeric or floating';

            }
            if($request->previous_gross_salary){
                $rules['previous_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['previous_gross_salary.regex']='input value must be numeric or floating';

            }
            if($request->new_gross_salary){
                $rules['new_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['new_gross_salary.regex']='input value must be numeric or floating';

            }
       $validateData = $request->validate( $rules, $message );
        $promotionInfo = PromotionInfo::findOrFail($id);
        $approval_doc_attachment = $promotionInfo->approval_doc_attachment;
        if($request->hasFile('approval_doc_attachment') && $request->approval_doc_attachment->isValid()){
            $file_path = public_path('upload/promotion4/'. $approval_doc_attachment);
            if(!empty($approval_doc_attachment) && file_exists($file_path)){
                unlink($file_path);
            }
            $approval_doc_attachment = Str::random(18).time() . '.' . $request->approval_doc_attachment->extension();
            $request->approval_doc_attachment->move(public_path('upload/promotion4'), $approval_doc_attachment);
        }

        $promotionInfo->update([

            'promotion_offer_issue_date' => $request->promotion_offer_issue_date,
            'effective_date' => $request->effective_date,
            'previous_designation' => $request->previous_designation,
            'promoted_designation' => $request->promoted_designation,
            'approval_doc_attachment' => $approval_doc_attachment,
            'increment_amount' => $request->increment_amount,
            'previous_basic' => $request->previous_basic,
            'new_basic' => $request->new_basic,
            'previous_gross_salary' => $request->previous_gross_salary,
            'new_gross_salary' => $request->new_gross_salary,
        ]);

        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    // ========================my profile Promotion Info update end=================
    public function trainingAssignmentUpdate(Request $request, $id){
        $rules =[
            'approval_doc_attachments'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


        ];
        $message = [

        ];
      $validateData = $request->validate( $rules, $message );

        $trainingAssignment = TrainingAssignment::findOrFail($id);
        $approval_doc_attachments = $trainingAssignment->approval_doc_attachments;
        if($request->hasFile('approval_doc_attachments') && $request->approval_doc_attachments->isValid()){
            $file_path = public_path('upload/training5/'.$approval_doc_attachments);
            if(!empty($approval_doc_attachments) && file_exists($file_path)){
                unlink($file_path);
            }
            $approval_doc_attachments= Str::random(10).time().'.'.$request->approval_doc_attachments->extension();
            //dd($approval_doc_attachments);
            $request->approval_doc_attachments->move(public_path('upload/training5'), $approval_doc_attachments);
        }

        $trainingAssignment->update([
            'training_program_name' => $request->training_program_name,
            'training_start_date' => $request->training_start_date,
            'training_end_date' => $request->training_end_date,
            'duration' => $request->duration,
            'location' => $request->location,
            'approval_doc_attachments' => $approval_doc_attachments,
            'about_training' => $request->about_training,
        ]);
        $notification = array(
            'message' => 'Update sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }
// ==================edit employee details from admin start============================

     // =======================employee update start ============================
     public function employeeUpdateFromAdmin(Request $request,$role,$id)
     {

        $rules =[
            'upload_cv' => 'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',

            'upload_image' => 'mimes:png,jpg, jpeg,image|max:2048',

            'employee_id'=>'required',
            'employee_name'=>'required',
            'employee_status'=>'required',
            'password'=>'required',
            // ==================employeess table validation end =============================
        ];
        $message =  [




        ];
      $validateData = $request->validate( $rules, $message );
         $accountInfo  = Employee::findOrFail($id);
         $upload_image = $accountInfo->upload_image;
         if($request->hasFile('upload_image') && $request->upload_image->isValid()){
             $file_path = public_path('upload/employee_img/'.$accountInfo->upload_image);
             if(!empty($upload_image) && file_exists($file_path)){
                 unlink($file_path);
             }
             $upload_image = time() . '.' . $request->upload_image->extension();
             $request->upload_image->move(public_path('upload/employee_img'), $upload_image);
         }

         $upload_cv = $accountInfo->upload_cv;
         if($request->hasFile('upload_cv') && $request->upload_cv->isValid())
         {
             $file_path = public_path('upload/employee_cv/'.$accountInfo->upload_cv);
             if( !empty($upload_cv) && file_exists($file_path)){
                 unlink($file_path);
             }
             $upload_cv = Str::random(10).time(). '.' . $request->upload_cv->extension();
             $request->upload_cv->move(public_path('upload/employee_cv'), $upload_cv);
         }

         $accountInfo->update([
             'employee_name' => $request->employee_name,
             'employee_id' => $request->employee_id,
             'designation' => $request->designation,
             'employee_status' => $request->employee_status,
             'username' => $request->username,
             'password' => bcrypt($request->password),
             'upload_image' => $upload_image,
             'upload_cv' => $upload_cv,
         ]);
         $notification = array(
             'message' => 'Update sucessfully',
             'alert-type' => 'success'
         );
         return redirect()->back()->with($notification);
     }


     // =======================employee update end ============================


     public function personalDetailsUpdateFromAdmin(Request $request, $role, $id)
     {

        $rules =[
            // ===============employees personal details table validation start ==============================
            'father_name'=>'required',
            'mother_name'=>'required',
            'permanent_address'=>'required',
            'date_of_birth'=>'required',
            'gender'=>'required',
            'martial_status'=>'required',
            'religion'=>'required',
            'nid_number'=>'required|max:17|min:10',
            'reference_info_1'=>'required',
            'relation_with_referal_1'=>'required',
            'reference_number_1'=>'required|max:11|min:11',
             // ===============employees personal details table validation end ==============================
          ];
          $message =  [


          ];
          if($request->birth_certificate_number){
              $rules['birth_certificate_number'] =['max:17|min:10'];

          }
          if($request->emergency_contact){
              $rules['emergency_contact'] =['max:11|min:11'];

          }
          if($request->mobile_number){
              $rules['mobile_number'] =['max:11|min:11'];

          }
          if($request->reference_number_2){
              $rules['reference_number_2'] =['max:11|min:11'];

          }

         $validateData = $request->validate( $rules, $message );
         // $Employee_Personal_Details_id = $request->id;
         $employee = Employee_Personal_Details::findOrFail($id)->update([
             'father_name' => $request->father_name,
             'mother_name' => $request->mother_name,
             'present_address' => $request->present_address,
             'permanent_address' => $request->permanent_address,
             'about_employee' => $request->about_employee,
             'date_of_birth' => $request->date_of_birth,
             'gender' => $request->gender,
             'martial_status' => $request->martial_status,
             'religion' => $request->religion,
             'mobile_number' => $request->mobile_number,
             'emergency_contact' => $request->emergency_contact,
             'nid_number' => $request->nid_number,
             'birth_certificate_number' => $request->birth_certificate_number,
             'passport_number' => $request->passport_number,
             'joining_date' => $request->joining_date,
             'bank_account_number' => $request->bank_account_number,
             'reference_info_1' => $request->reference_info_1,
             'reference_info_2' => $request->reference_info_2,
            'reference_number_1' => $request->reference_number_1,
            'reference_number_2' => $request->reference_number_2,
            'relation_with_referal_1' => $request->relation_with_referal_1,
            'relation_with_referal_2' => $request->relation_with_referal_2,

         ]);
         $notification = array(
             'message' => 'Update sucessfully',
             'alert-type' => 'success'
         );
         return redirect()->back()->with($notification);
     }



     public function educationUpdateFromAdmin(Request $request,$role, $id){

        $rules =[
            // ===============employees education and training  table validation start ==============================
            'degree_name'=>'required',
            'university'=>'required',
            'passing_year'=>'required',
            'result'=>['required','regex:/^-?(?:\d+|\d*\.\d{1,2})$/'],
            'upload_certificate'=>'required|mimes:png,jpg,doc,docx,jpeg,pdf|max:2048image|max:2048',
            'training_upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
            'upload_document'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',
            // ===============employees education and training  table validation start ==============================
          ];
          $message =  [


              'result.required'=>'You have to input floating value',
              'result.regex'=>'input value must be numeric or floating ',

          ];
     $validateData = $request->validate( $rules, $message );


             // dd($request->all());
             $educationAndTraning = EducationAndTraining::findOrFail($id);
             $upload_certificate = $educationAndTraning->upload_certificate;
             if($request->hasFile('upload_certificate') && $request->upload_certificate->isValid()){
                 $file_path = public_path('upload/education1/'.$upload_certificate);
                 if(!empty($upload_certificate) && file_exists($file_path)){
                     unlink($file_path);
                 }
                 $upload_certificate = Str::random(12).time() . '.' . $request->upload_certificate->extension();
                 $request->upload_certificate->move(public_path('upload/education1'), $upload_certificate);

             }
             $training_upload_document = $educationAndTraning->training_upload_document;

             if($request->hasFile('training_upload_document') && $request->training_upload_document->isValid()){
                 $file_path = public_path('upload/education2/'.$training_upload_document);
                 if( !empty($training_upload_document) && file_exists($file_path)){
                     unlink($file_path);
                 }
                 $training_upload_document = Str::random(14).time() . '.' . $request->training_upload_document->extension();
                 $request->training_upload_document->move(public_path('upload/education2'), $training_upload_document);
             }


             $upload_document = $educationAndTraning->upload_document;
             if($request->hasFile('upload_document') && $request->upload_document->isValid()){
                 $file_path = public_path('upload/education3/'. $upload_document);
                 if( !empty($upload_document) && file_exists($file_path)){
                     unlink($file_path);
                 }
                 $upload_document = Str::random(16).time() . '.' . $request->upload_document->extension();
                 $request->upload_document->move(public_path('upload/education3'), $upload_document);
             }

             $educationAndTraning->update([

                 'degree_name' => $request->degree_name,
                 'university' => $request->university,
                 'passing_year' => $request->passing_year,
                 'result' => $request->result,
                 'upload_certificate' => $upload_certificate,
                 'major_subject_name' => $request->major_subject_name,
                 'achieved_reward' => $request->achieved_reward,
                 'training_name' => $request->training_name,
                 'training_institute_name' => $request->training_institute_name,
                 'training_topic' => $request->training_topic,
                 'training_duration' => $request->training_duration,
                 'training_period' => $request->training_period,
                 'training_location' => $request->training_location,
                 'training_upload_document' => $training_upload_document,
                 'company_name' => $request->company_name,
                 'company_responsibility' => $request->company_responsibility,
                 'experience_area' => $request->experience_area,
                 'duration' => $request->duration,
                 'from_date' => $request->from_date,
                 'end_date' => $request->end_date,
                 'company_location' => $request->company_location,
                 'upload_document' => $upload_document,

             ]);



             $notification = array(
                 'message' => 'Update sucessfully',
                 'alert-type' => 'success'
             );
             return redirect()->back()->with($notification);

     }




     public function promotionInfoUpdateFromAdmin(Request $request, $role, $id){
         $rules=[
            'approval_doc_attachment'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


         ];
         $message = [

            ];
            if($request->increment_amount){
                $rules['increment_amount'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['increment_amount.regex']='input value must be numeric or floating';

            }
            if($request->new_basic){
                $rules['new_basic'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['new_basic.regex']='input value must be numeric or floating';

            }
            if($request->previous_gross_salary){
                $rules['previous_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['previous_gross_salary.regex']='input value must be numeric or floating';

            }
            if($request->new_gross_salary){
                $rules['new_gross_salary'] =['regex:/^-?(?:\d+|\d*\.\d{1,2})$/'];
                $message['new_gross_salary.regex']='input value must be numeric or floating';

            }
       $validateData = $request->validate( $rules, $message );


         $promotionInfo = PromotionInfo::findOrFail($id);
         $approval_doc_attachment = $promotionInfo->approval_doc_attachment;
         if($request->hasFile('approval_doc_attachment') && $request->approval_doc_attachment->isValid()){
             $file_path = public_path('upload/promotion4/'. $approval_doc_attachment);
             if( !empty($approval_doc_attachment) && file_exists($file_path)){
                 unlink($file_path);
             }
             $approval_doc_attachment = Str::random(18).time() . '.' . $request->approval_doc_attachment->extension();
             $request->approval_doc_attachment->move(public_path('upload/promotion4'), $approval_doc_attachment);
         }

         $promotionInfo->update([

             'promotion_offer_issue_date' => $request->promotion_offer_issue_date,
             'effective_date' => $request->effective_date,
             'previous_designation' => $request->previous_designation,
             'promoted_designation' => $request->promoted_designation,
             'approval_doc_attachment' => $approval_doc_attachment,
             'increment_amount' => $request->increment_amount,
             'previous_basic' => $request->previous_basic,
             'new_basic' => $request->new_basic,
             'previous_gross_salary' => $request->previous_gross_salary,
             'new_gross_salary' => $request->new_gross_salary,
         ]);

         $notification = array(
             'message' => 'Update sucessfully',
             'alert-type' => 'success'
         );
         return redirect()->back()->with($notification);
     }

     // ========================my profile Promotion Info update end=================
     public function trainingAssignmentUpdateFromAdmin(Request $request, $role, $id){

        $rules =[
            'approval_doc_attachments'=>'mimes:png,jpg,doc,docx,jpeg,pdf|max:2048,image|max:2048',


        ];
        $message = [

        ];
      $validateData = $request->validate( $rules, $message );

         $trainingAssignment = TrainingAssignment::findOrFail($id);
         $approval_doc_attachments = $trainingAssignment->approval_doc_attachments;
         if($request->hasFile('approval_doc_attachments') && $request->approval_doc_attachments->isValid()){
             $file_path = public_path('upload/training5/'.$approval_doc_attachments);
             if(!empty($approval_doc_attachments) && file_exists($file_path)){
                 unlink($file_path);
             }
             $approval_doc_attachments= Str::random(10).time().'.'.$request->approval_doc_attachments->extension();
             //dd($approval_doc_attachments);
             $request->approval_doc_attachments->move(public_path('upload/training5'), $approval_doc_attachments);
         }

         $trainingAssignment->update([
             'training_program_name' => $request->training_program_name,
             'training_start_date' => $request->training_start_date,
             'training_end_date' => $request->training_end_date,
             'duration' => $request->duration,
             'location' => $request->location,
             'approval_doc_attachments' => $approval_doc_attachments,
             'about_training' => $request->about_training,
         ]);
         $notification = array(
             'message' => 'Update sucessfully',
             'alert-type' => 'success'
         );
         return redirect()->back()->with($notification);
     }

// ==================edit employee details from admin end============================
    // =============employee leave ====================================
    public function leave(){
        $user_id=$this->get_user_id();
        $employees=Employee::all();
        return view('employee.employee-leave',compact('employees','user_id'));
    }
    //================ employee leave ends================================

   // =============employee leave request====================================
    public function leave_request(Request $request){
        $this->validate($request,[
            'file' => 'required|mimes:png,jpg,jpeg,csv,txt,xlx,xls,pdf|max:2048',
        ]);
        $fileName = time().'.'.$request->file->extension();
        $request->file->move(public_path('files'), $fileName);
        UserLeaveRequest::create([
            'employee_id' => $request->employee_id,
            'designation' => $request->designation,
            'date_from' => $request->date_from,
            'time_from' => $request->time_from,
            'to_date' => $request->to_date,
            'to_time' => $request->to_time,
            'department' => $request->department,
            'reason' => $request->reason,
            'description' => $request->description,
            'file' => $fileName,
        ]);
        $notification = array(
            'message' => 'leave request added sucessfully',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    //================ leave request list starts =====================================
    public function leave_request_list(){
        $leave_lists=UserLeaveRequest::all();
        return view('employee.employee-leave-request',compact('leave_lists'));
    }
    //===================leave request approval starts================================
    public function leave_request_approval($id){

        $approval=UserLeaveRequest::where('employee_id',$id)->first();
        $approval->approve=1;
        $approval->save();
        $notification = array(
            'message' => 'Request has been approved',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }

    //================== leave request destroy starts=================================
    public function leave_request_destroy($id){
        $approval=UserLeaveRequest::where('employee_id',$id)->first();
        $approval->delete();
        $notification = array(
            'message' => 'The request is deleted',
            'alert-type' => 'success'
        );
        return redirect()->back()->with($notification);
    }
    //================= leave request destroy ends=====================================


    //================ get user id starts ==============================================
    public function get_user_id(){
        $user=Auth::guard('employee')->user()->employee_id;
        return $user;
    }
    //===================get user id ends==================================



    // =============employee ticket project===================================
    public function ticket(){
        return view('employee.employee-tikets');
    }
    // =============employee attendance ====================================

    public function attendance(){
        return view('employee.employee-attendance');
    }


    public function getEmployeeWorkTimeReport(Request $request,$employee_id,$year){
        $employee = Employee::find($employee_id);
        if(!$employee){
            return response()->json(['error'=>"Employee Not Found"],404);
        }
        $total_work_permonths = Attendance::where('employee_id',$employee->employee_id)
                                           ->whereYear('date',$year)
                                           ->select('total_hours','date','total_late_in','total_over_time')
                                           ->get()
                                           ->groupBy(function($item){
                                                return date('M',strtotime($item->date));
                                           });
       return response()->json(compact('total_work_permonths'));
    }

    public function getWeeksOfMonth($employee_id,$year,$month){
        $employee = Employee::find($employee_id);
        if(!$employee){
            return response()->json(['error'=>"Employee Not Found"],404);
        }
        $holidaysInMonths = Holiday::whereYear('holiday_start_date',date('Y'))
                                   ->whereMonth('holiday_end_date',$month)
                                   ->orWhereMonth('holiday_start_date',$month)
                                   ->select('holiday_start_date','holiday_end_date')
                                   ->get()->toArray();
        $leavesInMonths = UserLeaveRequest::where(function($query) use($month,$year,$employee){
                                                $query->whereYear('date_from',$year)
                                                 ->whereMonth('date_from',$month)
                                                 ->where('employee_id',$employee->id);
                                            })->orWhere(function($query) use($month,$employee){
                                                $query->WhereMonth('to_date',$month)
                                                ->where('employee_id',$employee->id);
                                            })
                                            ->select('time_from','date_from','to_date','to_time','employee_id')
                                            ->get()->toArray();
        $attendances = Attendance::where('employee_id',$employee->employee_id)
                                   ->where(function($query) use($year){
                                        $query->whereYear('date',$year);
                                    })
                                    ->whereMonth('date',date('m',strtotime($month)))
                                    ->select('total_hours','date','total_late_in','total_over_time','employee_id')
                                    ->get()->toArray();
        return response()->json(compact('holidaysInMonths','leavesInMonths','attendances'));
    }

    function getWeeks($date ) //2022-march-t
    {
        $cut        = substr($date, 0, 8);
        $daylen     = 86400;
        $timestamp  = strtotime($date);
        $first      = strtotime($cut . "00");
        $elapsed    = ($timestamp - $first) / $daylen;
        $weeks      = 1;
        $quarter    = [];
        $key        = 0;
        for ($i = 1; $i <= $elapsed; $i++)
        {
            $dayfind      = $cut . (strlen($i) < 2 ? '0' . $i : $i);
            $daytimestamp = strtotime($dayfind);
            $day = strtolower(date("l", $daytimestamp));
            $quarter[$key][] = $day;
            if($i % 7 == 0){
               $key++;
            }
            // if($day == strtolower($rollover))  $weeks ++;
        }
        return $quarter;
    }




    // =============employee leave ====================================

    // =============employee calender ====================================
    public function calender(){
        return view('employee.calender');
    }

    // =============employee task list ====================================
    public function tasklist($project){

        $tasks = Task::join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
                ->join('employees', 'assigned_to_tasks.employee_id', '=', 'employees.id')
                ->where('employees.id',Auth::guard('employee')->user()->id)
                ->where('project_id',$project)
                ->get();


        $todo = Task::where('project_id',$project)->where('status',1)
        ->join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
        ->where('employee_id',Auth::guard('employee')->user()->id)->get();

        $inProgress = Task::where('project_id',$project)->where('status',2)
        ->join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
        ->where('employee_id',Auth::guard('employee')->user()->id)->get();


        $testing = Task::where('project_id',$project)->where('status',3)
        ->join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
        ->where('employee_id',Auth::guard('employee')->user()->id)->get();

        $done = Task::where('project_id',$project)->where('status',4)
        ->join('assigned_to_tasks', 'tasks.id', '=', 'assigned_to_tasks.task_id')
        ->where('employee_id',Auth::guard('employee')->user()->id)->get();

        // dd($todo);

        return view('employee.task-list',compact('tasks','todo','inProgress','testing','done'));
    }

    public function getSingleTaskDetails($id)
    {
        $task = Task::find($id);
        return $task;
    }


    public function setTime(Request $request){
        $user=Auth::guard('employee')->user();
        $user_id=$user->id;
        $check_timer=TimeScheduler::where('task_id',$request->task_id)->where('status',0)->where('user_id',$user_id)->get();
        if(count($check_timer)==0){
            TimeScheduler::create([
                'user_id'=> $user_id,
                'start_time' => Carbon::now()->format('h:i:s'),
                'task_id' => $request->task_id,
            ]);

        }else{
           dd("You are already runninng a task.please Quit");
        }

    }
    public function endTime(Request $request){
        $user=Auth::guard('employee')->user();
        $user_id=$user->id;
        $timer=TimeScheduler::where('task_id',$request->task_id)->where('status',0)->where('user_id',$user_id)->latest()->first();
        // if(!$timer){
        //     TimeScheduler::create([
        //        'status' =>1,
        //     ]);
        // }
        $timer->end_time = Carbon::now()->format('h:i:s');
        $start_time = strtotime($timer->start_time);
        $end_time = strtotime(Carbon::now()->format('h:i:s'));

        $timer->duration = $end_time-$start_time;
        $timer->status = 1;
        $timer->update();
    }


    public function getTime($taskid)
    {
        try{
            $user=Auth::guard('employee')->user();
            $user_id=$user->id;
            $getTime = TimeScheduler::where('task_id',$taskid)->where('status',0)->where('user_id',$user_id)->latest()->first();
            if(!$getTime){
               return response()->json(['error'=>"not Found"],404);
            }
            return response()->json($getTime);
        }catch(Exception $e){
            return response()->json(['error'=>"not Found"],404);
        }
    }


    public function pieChart()
    {
        $task = Task::join('assigned_to_tasks','tasks.id','=','assigned_to_tasks.task_id')
                ->where('assigned_to_tasks.employee_id',Auth::guard('employee')->user()->id)
                ->select(DB::raw('status , ifnull(count(*),0) as count'))
                ->groupBy('tasks.status')
                ->orderBy('status','ASC')
                ->pluck('count','status');

        return response()->json($task);


    }

    public function donuteChart()
    {
        $projects = AddProject::select(DB::raw('add_projects.project_status , ifnull(count(*),0) as count'))
        ->groupBy('project_status')
        ->orderBy('project_status','ASC')
        ->pluck('count','project_status');

        $arr = array(10,20);

        return response()->json($projects);
    }




}
<!-- =========================employee end======================================== -->
