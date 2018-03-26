# -/*
 * 数独
 *
 *
 *
 */
class Sudoku {
    var $matrix;

    function __construct($arr = null) {
        if ($arr == null) {
            $this->clear();
        } else {
            $this->matrix = $arr;
        }
    }
    
    function clear() {
        for($i=0; $i<9; $i++) {
            for($j=0; $j<9; $j++) {
                $this->matrix[$i][$j] = array();
                for ($k = 1; $k <= 9; $k++) {
                    $this->matrix[$i][$j][$k] = $k;
                }
            }
        }
    }

    function setCell($row, $col, $value){
        $this->matrix[$row][$col] = array($value => $value);
        //row
        for($i = 0; $i < 9; $i++){
            if($i != $col){
                if(! $this->removeValue($row, $i, $value)) {
                    return false;
                }
            }
        }
        //col
        for($i = 0; $i < 9; $i++){
            if($i != $row){
                if(! $this->removeValue($i, $col, $value)) {
                    return false;
                }
            }
        }
        //square
        $rs=intval($row / 3) * 3;
        $cs=intval($col / 3) * 3;

        for($i = $rs; $i < $rs + 3; $i++){
            for($j = $cs; $j < $cs + 3; $j++){
                if($i != $row && $j != $col){
                    if(! $this->removeValue($i, $j, $value))
                        return false;
                }
            }
        }
        return true;
    }
    
    function removeValue($row, $col, $value) {
        $count = count($this->matrix[$row][$col]);
        if($count == 1){
            $ret = !isset($this->matrix[$row][$col][$value]);
            return $ret;
        }
        if (isset($this->matrix[$row][$col][$value])) {
            unset($this->matrix[$row][$col][$value]);
            if($count - 1 == 1) {
                return $this->setCell($row, $col, current($this->matrix[$row][$col]));
            }
        }
        return true;
    }
    
    function set($arr) {
        for ($i = 0; $i < 9; $i++) {
            for ($j = 0; $j < 9; $j++) {
                if ($arr[$i][$j] > 0) {
                    $this->setCell($i, $j, $arr[$i][$j]);
                }
            }
        }
    }

    function dump() {
        for($i = 0; $i < 9; $i++){
            for($j = 0; $j < 9; $j++){
                $c = count($this->matrix[$i][$j]);
                if($c == 1){
                    echo " ".current($this->matrix[$i][$j])." ";
                } else {
                    echo "(".$c.")";
                }
            }
            echo "\n";
        }
        echo "\n";
    }

    function dumpAll() {
        for($i = 0; $i < 9; $i++){
            for($j = 0; $j < 9; $j++){
                echo implode('', $this->matrix[$i][$j]), "\t";
            }
            echo "\n";
        }
        echo "\n";
    }

    function calc($data) {
        $this->clear();
        $this->set($data);
        $this->_calc();
        $this->dump();
    }

    function _calc() {
        for($i = 0; $i < 9; $i++){
            for($j = 0; $j < 9; $j++){
                if(count($this->matrix[$i][$j]) == 1) {
                    continue;
                }
                foreach($this->matrix[$i][$j] as $v){
                    $flag = false;
                    $t = new Sudoku($this->matrix);
                    if(!$t->setCell($i, $j, $v)){
                        continue;
                    }
                    if(!$t->_calc()){
                        continue;
                    }
                    $this->matrix = $t->matrix;
                    return true;
                }
                return false;
            }
        }
        return true;
    }
}

$sd=new Sudoku;
$sd->calc(array(
array(1,6,0,0,4,0,7,0,8),
array(0,0,7,3,2,0,0,0,9),
array(0,2,0,7,0,0,5,0,0),
array(5,0,0,8,0,0,3,0,7),
array(3,0,0,9,1,0,0,0,5),
array(6,0,8,0,0,3,0,0,2),
array(0,0,6,0,0,5,0,7,0),
array(7,0,0,0,3,8,2,0,0),
array(2,0,9,0,7,0,0,5,6),
));
