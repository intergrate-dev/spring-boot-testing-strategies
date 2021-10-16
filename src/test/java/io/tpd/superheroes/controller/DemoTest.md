@Slf4j
@SpringBootTest
class MqsDataParseServiceImplTest {

    @Resource
    DemoService demoService;

    @Test
    void testService() {
        String json = FileUtil.readString("D:\\xxx\\parse.json", StandardCharsets.UTF_8);
        demoService.method();
    }
}


@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class ControllerMockTest {

    private MockMvc mockMvc;
  
    @Autowired
    private WebApplicationContext webApplicationContext;

    @Before
    public void setUp() throws Exception {
        mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
    }
    
    @Test
    @DisplayName("excel导出")
    public void mockExcelExport() throws Exception{
        // when
        // ResponseEntity<SuperHero> superHeroResponse = restTemplate.getForEntity("/superheroes/2", SuperHero.class);
        // ResponseEntity<ResponseResult> response = restTemplate.getForEntity("/excel/export", ResponseResult.class);

        // then
        // assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        Integer[] ids = {3451,3673,3157,2509};
        mockMvc.perform(post("/excel/export")
                .contentType(MediaType.APPLICATION_FORM_URLENCODED))
                .andExpect(status().isOk())
                .andDo(new ResultHandler() {
                    @Override
                    public void handle(MvcResult mvcResult) throws Exception {
                        //保存为文件
                        File file  = new File("/data/file/tool-export.xls");
                        file.delete();
                        FileOutputStream fout = new FileOutputStream(file);
                        ByteArrayInputStream bin = new ByteArrayInputStream(mvcResult.getResponse().getContentAsByteArray());
                        StreamUtils.copy(bin,fout);
                        fout.close();
                        System.out.println("is exist:"+file.exists());
                        //assert
                        System.out.println("file length:"+file.length());
                        assertThat(file.exists(), equalTo(true));
                        assertThat(file.length(), greaterThan(1024L));
                    }
                });
    }
  }
