
data-expire="{{Carbon\Carbon::parse($sale->sale_date)->format( 'Y/m/d h:m:s')}}"
<!-- ======= -->
distint working procedure
<!-- ============for product cont condition -->
@if($product->count()>0 && $sale->status ==1 && $sale->sale_date > Carbon\Carbon::now())

@endif
<!-- ============end product count condition -->
