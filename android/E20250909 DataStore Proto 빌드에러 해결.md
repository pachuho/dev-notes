## DataStore-Proto 빌드 시 에러 발생
### 배경
- 동적 생성이 필요한 .proto 파일 빌드 시 에러가 발생한다.
- 에러 코드
   ```kotlin
    e: [ksp] InjectProcessingStep was unable to process 'HomeDataSourceImpl(androidx.datastore.core.DataStore<error.NonExistentClass>)' because 'error.NonExistentClass' could not be resolved. Dependency trace: => element (CLASS):

### 원인
- KSP 필요한 파일(Proto DataStore가 생성한 파일)이 아직 준비되지 않다.
- Gradle은 빌드 속도를 높이기 위해 병렬 빌드를 진행 하는데, 작업 간의 순서가 명확하게 설정되어 있지 않다.

### 해결
- `withType<KspTask>` 블럭 하위에서 Proto 객체 생성 태스크가 모두 완료된 이후 KSP 플러그인이 실행이 실행되도록 설정하여 타이밍 문제를 해결한다.
- `:data:source` 모듈 내의 `build.gradle.kts` 하단에 해당 코드 추가
    ```kotlin
    tasks.withType<KspTask>().configureEach {
        val variantName = name.removePrefix("ksp").removeSuffix("Kotlin")
        val protoGenTaskName = "generate${variantName}Proto"
    
        tasks.findByName(protoGenTaskName)?.let {
            dependsOn(it)
        }
    }
    ```