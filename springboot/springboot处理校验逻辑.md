#### SpringBoot�д���У���߼������ַ�ʽ��
#ƽʱ�ڿ����ӿڵ�ʱ�򣬳�������Ҫ�Բ�������У�飬�����ṩ���ִ���У���߼��ķ�ʽ��һ����ʹ��Hibernate Validator��������һ����ʹ��ȫ���쳣�������������ǽ��������ַ�ʽ���÷���

**************************Ҽ****************************
ʲô��Hibernate Validator��
#Hibernate Validator��SpringBoot���õ�У���ܣ�ֻҪ������SpringBoot���Զ��������������ǿ���ͨ���ڶ�������ʹ�����ṩ��ע������ɲ���У�顣

һ������ע��
---- @Null����ע�͵����Ա���Ϊnull��
---- @NotNull����ע�͵����Բ���Ϊnull��
---- @AssertTrue����ע�͵����Ա���Ϊtrue��
---- @AssertFalse����ע�͵����Ա���Ϊfalse��
---- @Min����ע�͵����Ա�����ڵ�����valueֵ��
---- @Max����ע�͵����Ա���С�ڵ�����valueֵ��
---- @Size����ע�͵����Ա�������min��maxֵ֮�䣻
---- @Pattern����ע�͵����Ա��������regexp�������������ʽ��
---- @NotBlank����ע�͵��ַ�������Ϊ���ַ�����
---- @NotEmpty����ע�͵����Բ���Ϊ�գ�
---- @Email����ע�͵����Ա�����������ʽ��

����ʹ�÷�ʽ
---- ��ʵ������ֶ�����ӳ���ע��
        @NotEmpty(message = "���Ʋ���Ϊ��")
        private String name;

---- Ȼ������ӵĽӿ������@Validatedע�⣬��ע��һ��BindingResult������
        public CommonResult create(@Validated @RequestBody PmsBrandParam pmsBrand, BindingResult result) {}

---- Ȼ��������Controller�㴴��һ�����棬���价��֪ͨ�л�ȡ��ע���BindingResult����ͨ��hasErrors�����ж�У���Ƿ�ͨ��������д�����Ϣֱ�ӷ��ش�����Ϣ����֤ͨ������У�
/**
 * HibernateValidator��������������
 * Created by macro on 2018/4/26.
 */
@Aspect
@Component
@Order(2)
public class BindingResultAspect {
    @Pointcut("execution(public * com.macro.mall.controller.*.*(..))")
    public void BindingResult() {
    }

    @Around("BindingResult()")
    public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
        Object[] args = joinPoint.getArgs();
        for (Object arg : args) {
            if (arg instanceof BindingResult) {
                BindingResult result = (BindingResult) arg;
                if (result.hasErrors()) {
                    FieldError fieldError = result.getFieldError();
                    if(fieldError!=null){
                        return CommonResult.validateFailed(fieldError.getDefaultMessage());
                    }else{
                        return CommonResult.validateFailed();
                    }
                }
            }
        }
        return joinPoint.proceed();
    }
}

�����Զ���ע��
#��ʱ�����ṩ��У��ע�Ⲣ�����������ǵ���Ҫ����ʱ���Ǿ���Ҫ�Զ���У��ע�⡣���绹����������Ʒ�ƣ���ʱ�и�����showStatus������ϣ����ֻ����0����1���������������֣���ʱ����ʹ���Զ���ע����ʵ�ָù��ܡ�

��1�������Զ���һ��У��ע����FlagValidator��Ȼ�����@Constraintע�⣬ʹ������validatedBy����ָ��У���߼��ľ���ʵ���ࣻ
/**
 * �û���֤״̬�Ƿ���ָ����Χ�ڵ�ע��
 * Created by macro on 2018/4/26.
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD,ElementType.PARAMETER})
@Constraint(validatedBy = FlagValidatorClass.class)
public @interface FlagValidator {
    String[] value() default {};

    String message() default "flag is not found";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}

��2��Ȼ�󴴽�FlagValidatorClass��ΪУ���߼��ľ���ʵ���࣬ʵ��ConstraintValidator�ӿڣ�������Ҫָ���������Ͳ�������һ����Ҫָ��Ϊ���Զ����У��ע���࣬�ڶ���ָ��Ϊ��ҪУ�����Ե����ͣ�isValid�����о��Ǿ����У���߼���
/**
 * ״̬���У����
 * Created by macro on 2018/4/26.
 */
public class FlagValidatorClass implements ConstraintValidator<FlagValidator,Integer> {
    private String[] values;
    @Override
    public void initialize(FlagValidator flagValidator) {
        this.values = flagValidator.value();
    }

    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext constraintValidatorContext) {
        boolean isValid = false;
        if(value==null){
            //��״̬Ϊ��ʱʹ��Ĭ��ֵ
            return true;
        }
        for(int i=0;i<values.length;i++){
            if(values[i].equals(String.valueOf(value))){
                isValid = true;
                break;
            }
        }
        return isValid;
    }
}

��3�����������ǾͿ����ڴ��ζ�����ʹ�ø�ע����
/**
 * Ʒ�ƴ��ݲ���
 * Created by macro on 2018/4/26.
 */
public class PmsBrandParam {

    @ApiModelProperty(value = "�Ƿ������ʾ")
    @FlagValidator(value = {"0","1"}, message = "��ʾ״̬����ȷ")
    private Integer showStatus;

   //ʡ������Getter��Setter����...
}

**************************��****************************
ȫ���쳣����
#ʹ��ȫ���쳣����������У���߼���˼·�ܼ򵥣�����������Ҫͨ��@ControllerAdviceע�ⶨ��һ��ȫ���쳣�Ĵ����࣬Ȼ���Զ���һ��У���쳣����������Controller��У��ʧ��ʱ��ֱ���׳����쳣�������Ϳ��ԴﵽУ��ʧ�ܷ��ش�����Ϣ��Ŀ���ˡ�

һ��ʹ�õ���ע��
---- @ControllerAdvice��������@Componentע�⣬����ָ��һ���������������Ҫ������ǿ@Controllerע�����ε���Ĺ��ܣ�����˵����ȫ���쳣����
---- @ExceptionHandler����������ȫ���쳣����ķ���������ָ���쳣�����͡�

����ʹ�÷�ʽ
---- ����������Ҫ�Զ���һ���쳣��ApiException��������У��ʧ��ʱ�׳����쳣��
/**
 * �Զ���API�쳣
 * Created by macro on 2020/2/27.
 */
public class ApiException extends RuntimeException {
    private IErrorCode errorCode;

    public ApiException(IErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }

    public ApiException(String message) {
        super(message);
    }

    public ApiException(Throwable cause) {
        super(cause);
    }

    public ApiException(String message, Throwable cause) {
        super(message, cause);
    }

    public IErrorCode getErrorCode() {
        return errorCode;
    }
}

---- Ȼ�󴴽�һ�����Դ�����Asserts�������׳�����ApiException
/**
 * ���Դ����࣬�����׳�����API�쳣
 * Created by macro on 2020/2/27.
 */
public class Asserts {
    public static void fail(String message) {
        throw new ApiException(message);
    }

    public static void fail(IErrorCode errorCode) {
        throw new ApiException(errorCode);
    }
}

---- Ȼ���ٴ������ǵ�ȫ���쳣������GlobalExceptionHandler�����ڴ���ȫ���쳣�������ط�װ�õ�CommonResult����
/**
 * ȫ���쳣����
 * Created by macro on 2020/2/27.
 */
@ControllerAdvice
public class GlobalExceptionHandler {

    @ResponseBody
    @ExceptionHandler(value = ApiException.class)
    public CommonResult handle(ApiException e) {
        if (e.getErrorCode() != null) {
            return CommonResult.failed(e.getErrorCode());
        }
        return CommonResult.failed(e.getMessage());
    }
}

---- �������û���ȡ�Ż�ȯ�Ĵ���Ϊ���������ȶԱ��¸Ľ�ǰ��Ĵ��룬���ȿ�Controller����롣�Ľ���ֻҪService�еķ���ִ�гɹ��ͱ�ʾ��ȡ�Ż�ȯ�ɹ�����Ϊ��ȡ���ɹ��Ļ���ֱ���׳�ApiException�Ӷ����ش�����Ϣ
/**
 * �û��Ż�ȯ����Controller
 * Created by macro on 2018/8/29.
 */
@Controller
@Api(tags = "UmsMemberCouponController", description = "�û��Ż�ȯ����")
@RequestMapping("/member/coupon")
public class UmsMemberCouponController {
    @Autowired
    private UmsMemberCouponService memberCouponService;

    //�Ľ�ǰ
    @ApiOperation("��ȡָ���Ż�ȯ")
    @RequestMapping(value = "/add/{couponId}", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult add(@PathVariable Long couponId) {
        return memberCouponService.add(couponId);
    }

    //�Ľ���
    @ApiOperation("��ȡָ���Ż�ȯ")
    @RequestMapping(value = "/add/{couponId}", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult add(@PathVariable Long couponId) {
        memberCouponService.add(couponId);
        return CommonResult.success(null,"��ȡ�ɹ�");
    }    
}

---- �ٿ���Service�ӿ��еĴ��룬�������ڷ��ؽ�����Ľ��󷵻ص���void����ʵCommonResult�����ñ�������Ϊ�˰�Service�л�ȡ�������ݷ�װ��ͳһ���ؽ�����Ľ�ǰ������Υ�������ԭ�򣬸Ľ�������������������⣻
/**
 * �û��Ż�ȯ����Service
 * Created by macro on 2018/8/29.
 */
public interface UmsMemberCouponService {
    /**
     * ��Ա����Ż�ȯ���Ľ�ǰ��
     */
    @Transactional
    CommonResult add(Long couponId);

    /**
     * ��Ա����Ż�ȯ���Ľ���
     */
    @Transactional
    void add(Long couponId);
}

---- �ٿ���Serviceʵ�����еĴ��룬���Կ���ԭ��У���߼��з���CommonResult���߼����ĳ��˵���Asserts��fail������ʵ�֣�
//�Ľ�ǰ
    @Override
    public CommonResult add(Long couponId) {
        UmsMember currentMember = memberService.getCurrentMember();
        //��ȡ�Ż�ȯ��Ϣ���ж�����
        SmsCoupon coupon = couponMapper.selectByPrimaryKey(couponId);
        if(coupon==null){
            return CommonResult.failed("�Ż�ȯ������");
        }
        if(coupon.getCount()<=0){
            return CommonResult.failed("�Ż�ȯ�Ѿ�������");
        }
        Date now = new Date();
        if(now.before(coupon.getEnableTime())){
            return CommonResult.failed("�Ż�ȯ��û����ȡʱ��");
        }
        //�ж��û���ȡ���Ż�ȯ�����Ƿ񳬹�����
        SmsCouponHistoryExample couponHistoryExample = new SmsCouponHistoryExample();
        couponHistoryExample.createCriteria().andCouponIdEqualTo(couponId).andMemberIdEqualTo(currentMember.getId());
        long count = couponHistoryMapper.countByExample(couponHistoryExample);
        if(count>=coupon.getPerLimit()){
            return CommonResult.failed("���Ѿ���ȡ�����Ż�ȯ");
        }
        //ʡ����ȡ�Ż�ȯ�߼�...
        return CommonResult.success(null,"��ȡ�ɹ�");
    }

    //�Ľ���
     @Override
     public void add(Long couponId) {
         UmsMember currentMember = memberService.getCurrentMember();
         //��ȡ�Ż�ȯ��Ϣ���ж�����
         SmsCoupon coupon = couponMapper.selectByPrimaryKey(couponId);
         if(coupon==null){
             Asserts.fail("�Ż�ȯ������");
         }
         if(coupon.getCount()<=0){
             Asserts.fail("�Ż�ȯ�Ѿ�������");
         }
         Date now = new Date();
         if(now.before(coupon.getEnableTime())){
             Asserts.fail("�Ż�ȯ��û����ȡʱ��");
         }
         //�ж��û���ȡ���Ż�ȯ�����Ƿ񳬹�����
         SmsCouponHistoryExample couponHistoryExample = new SmsCouponHistoryExample();
         couponHistoryExample.createCriteria().andCouponIdEqualTo(couponId).andMemberIdEqualTo(currentMember.getId());
         long count = couponHistoryMapper.countByExample(couponHistoryExample);
         if(count>=coupon.getPerLimit()){
             Asserts.fail("���Ѿ���ȡ�����Ż�ȯ");
         }
         //ʡ����ȡ�Ż�ȯ�߼�...
     }


�ο���http://www.macrozheng.com/#/technology/springboot_validator